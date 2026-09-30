# write-tests — stack reference

Load the section for the stack under test. Examples are shapes, not templates to copy.

## TypeScript / NestJS / Jest

### HTTP seam with the production pipe

Most boundary bugs live in validation, and a controller method called directly skips it. Build a
testing app with the **same `ValidationPipe` options as `main.ts`** (export them from one place so
the test cannot drift) and drive it with `supertest`:

```ts
const app: INestApplication = ( await Test.createTestingModule( {
	controllers: [ SettingsController ],
	providers: [ UpdateSettingsUseCase, { provide: SettingsService, useValue: settings } ],
} ).compile() ).createNestApplication();

app.useGlobalPipes( new ValidationPipe( VALIDATION_PIPE_OPTIONS ) );
await app.init();

await request( app.getHttpServer() ).post( '/settings' ).send( { enabled: null } ).expect( 400 );
```

Use it for: status codes, `null`/malformed/unknown fields, guards, the response shape a client
parses. Keep business branches at the use-case seam — one HTTP test per boundary concern, not one
per branch.

### Factory per seam

```ts
interface Deps { store?: FakeStore; clock?: FakeClock; locked?: boolean }

const makeUseCase = ( deps: Deps = {} ): { useCase: UpdateSettingsUseCase; store: FakeStore } => {

	const store: FakeStore = deps.store ?? new FakeStore( DEFAULT_SETTINGS );
	const settings: SettingsService = new SettingsService( ENV, store );

	if ( deps.locked ) settings.lock();

	return { useCase: new UpdateSettingsUseCase( settings ), store };

};
```

Named options, real collaborators by default, one place to change when the constructor does.
Shared fakes live in `test/`, never `src/` (`conventions/testing.md`).

### Observables

Assert what a subscriber sees: `firstValueFrom` for single emissions, a collected array for
streams. A fake of a cold seam must be cold too — a hot fake lets a test pass while the code under
test never subscribed.

### Anti-patterns seen in real suites

| Pattern | Why it churns | Instead |
|---|---|---|
| `new Service( {} as any, {} as any, … x16, fake )` | Breaks on every constructor change | Factory with named options |
| `for ( key of [ …every field ] ) expect( hasOwnProperty )` | Fails on every field removal, never on a wrong value | Assert the values a consumer reads |
| `toThrow( /fragment/ )` in many tests | Fails on rewording | Assert the refusal type/kind; whole sentence once, where it is the contract |
| Calling a controller method directly for validation cases | Skips the pipe; `@IsOptional` lets `null` through unnoticed | HTTP seam with the production pipe |
| `expect( fake.method ).toHaveBeenCalled()` as the only assertion | Proves the wiring, not the outcome | Assert the state or output the call produces |

---
name: bun
description: Use Bun CLI and APIs instead of node, npm, npx, pnpm, yarn, deno, vite, vitest, jest, exec, fs, ws, hash, sha, sqlite, redis. Also supports gzip, dns, HTML/MD/YAML parsing, workers ++
user-invocable: true
---
Bun loads .env file, do NOT use `dotenv`
Bun runs typescript natively, do NOT use `tsc`

# CLI commands
`bun init -y` blank
`bun init --react=tailwind`
`bun init --react=shadcn` tailwind + shadcn

`bun add/install ...`
`bun run ...` instead of `npm run ...`
`bun file.ts` instead of `tsc file.ts && node file.js`
`bun test` instead of `jest` or `vitest`
`bun test --coverage`
`bun x ...` instead of `npx ...`
`bun create ...` instead of `vite create ...`

# All APIs
`Bun.serve`, `$`, `Bun.build`, `Bun.file`, `Bun.write`, `Bun.stdin`, `Bun.stdout`, `Bun.stderr`, `Bun.spawn`, `Bun.spawnSync`, `Bun.listen`, `Bun.connect`, `Bun.udpSocket`, `new WebSocket()`, `Bun.Transpiler`, `Bun.FileSystemRouter`, `HTMLRewriter`, `Bun.password`, `Bun.hash`, `Bun.CryptoHasher`, `Bun.sha`, `bun:sqlite`, `Bun.SQL`, `Bun.sql`, `Bun.RedisClient`, `Bun.redis`, `bun:ffi`, `Bun.dns.lookup`, `Bun.dns.prefetch`, `Bun.dns.getCacheStats`, `bun:test`, `new Worker()`, `Bun.plugin`, `Bun.Glob`, `Bun.Cookie`, `Bun.CookieMap`, `Bun.version`, `Bun.revision`, `Bun.env`, `Bun.main`, `Bun.sleep()`, `Bun.sleepSync()`, `Bun.nanoseconds()`, `Bun.randomUUIDv7()`, `Bun.which()`, `Bun.peek()`, `Bun.deepEquals()`, `Bun.deepMatch`, `Bun.inspect()`, `Bun.escapeHTML()`, `Bun.stringWidth()`, `Bun.indexOfLine`, `Bun.fileURLToPath()`, `Bun.pathToFileURL()`, `Bun.gzipSync()`, `Bun.gunzipSync()`, `Bun.deflateSync()`, `Bun.inflateSync()`, `Bun.zstdCompressSync()`, `Bun.zstdDecompressSync()`, `Bun.zstdCompress()`, `Bun.zstdDecompress()`, `Bun.readableStreamTo*()`, `Bun.readableStreamToBytes()`, `Bun.readableStreamToBlob()`, `Bun.readableStreamToFormData()`, `Bun.readableStreamToJSON()`, `Bun.readableStreamToArray()`, `Bun.ArrayBufferSink`, `Bun.allocUnsafe`, `Bun.concatArrayBuffers`, `Bun.resolveSync()`, `Bun.semver`, `Bun.TOML.parse`, `Bun.markdown`, `Bun.color`, `Bun.mmap`, `Bun.gc`, `Bun.generateHeapSnapshot`, `bun:jsc`
 
# Examples for common APIs
## [`Bun.serve`](https://bun.sh/docs/api/http)
```ts
const server = Bun.serve({
  port: 3000,
  routes: {
    "/users/:id": req => new Response(`User ${req.params.id}`),
  },
  fetch(req) {
    return new Response("Not found", { status: 404 });
  },
});
```

## [`$`](https://bun.sh/docs/runtime/shell)
```ts
const branch = await Bun.$`git rev-parse --abbrev-ref HEAD`.text();
```

## [`Bun.build`](https://bun.sh/docs/bundler)
```ts
const result = await Bun.build({
  entrypoints: ["./src/index.ts"],
  outdir: "./dist",
  minify: true,
});
```

## [`Bun I/O`](https://bun.sh/docs/api/file-io#stdin-stdout-stderr)
```ts
// Bun.file returns a lazy `BunFile` (`Blob` subclass)
const file = Bun.file("./config.json");
const config = await file.json();
await Bun.write("./output.txt", "hello world");

const input = await Bun.stdin.text();
await Bun.write(Bun.stdout, Bun.file("./README.md"));
await Bun.write(Bun.stderr, "Something went wrong\n");
```

## [`Bun.env`](https://bun.sh/docs/runtime/env)
Alias for `process.env` / `import.meta.env`.
```ts
const port = Bun.env.PORT ?? "3000";
```

## [`Bun.semver`](https://bun.sh/docs/api/semver)
```ts
import { semver } from "bun";
semver.satisfies("1.2.3", "^1.0.0"); // true
["1.0.1", "1.0.0-alpha", "1.0.0"].sort(semver.order);
```

## [`Bun.TOML.parse`](https://bun.sh/docs/runtime/toml)
```ts
const config = Bun.TOML.parse(`
[server]
port = 3000
`);
```

## [`Bun.markdown`](https://bun.sh/docs/api/markdown)
CommonMark + GFM parser. Renders to HTML by default; pass component callbacks to produce any format.
```ts
const html = Bun.markdown.render("# Hello\n\n**world**");
const plain = Bun.markdown.render("**bold**", {
  strong: children => children,
  paragraph: children => children + "\n",
});
```

## [`Bun.color`](https://bun.sh/docs/api/color)
```ts
Bun.color("hsl(120, 100%, 50%)", "css");
Bun.color("#ff6600", "ansi-16m");
Bun.color({ r: 0, g: 128, b: 255 }, "hex");
```
Returns `null` on invalid input.

# Fallback to node APIs
Bun runtime implements most of `node:*` APIs. Fallback to these if necessary.
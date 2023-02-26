# Bun Plugin Auto Import

Auto import support for [Bun](https://bun.sh), powered by [Unimport](https://github.com/unjs/unimport).

## Install

```bash
bun add bun-auto-import@latest -d
```

And then add the following code to the beginning of your entry file.

```ts
import { plugin } from "bun"

plugin(
    (await import("bun-auto-import")).autoImport({
        // Options
        // For example:
        presets: ["solid-js"],
        imports: [{ name: "z", from: "zod" }],

        // The generated .d.ts file path
        // Default: `./auto-import.d.ts`
        dts: `./src/auto-import.d.ts`,
    })
)
```

See more options at [Unimport's configuration documentation](https://github.com/unjs/unimport#configurations).

After that, you can use the amazing Auto Import in other files (expect for your bun entry file).

For example:

```ts
// index.ts
import { plugin } from "bun"
import { handler } from "./server"

plugin(
	(await import("bun-auto-import")).autoImport({
		imports: [{ name: "z", from: "zod" }],
	})
)

Bun.serve({
	fetch: handler,
	port: 3000,
})

// server.ts
// `z` is auto imported from zod
const Body = z.object({
	msg: z.string(),
})

export const handler = async (req: Request) => {
	try {
		const body = await req.json()
		const data = Body.parse(body)
		return new Response(`Received: ${data.msg}`)
	} catch (e) {
		return new Response("Invalid body", { status: 400 })
	}
}
```

Then run `bun index.ts` and `curl http://localhost:3000/ --data "{\"msg\":\"Hello Auto Import\"}"`.
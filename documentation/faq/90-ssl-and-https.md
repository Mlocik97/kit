---
question: How do I make Svelte-kit run on HTTPS using node adapter?
---

First, You will need to get certificate. You can use Let's encrypt and generate certificate by following [certbot](https://certbot.eff.org/lets-encrypt/snap-webproduct) instructions, or generate Your own certificate with command:

```sh
openssl req -newkey rsa:4096 \
            -x509 \
            -sha256 \
            -days 3650 \
            -nodes \
            -out cert.crt \
            -keyout key.key
```

_Note1: Your own certificate will not be trusted by browsers on other machines. It's strongly recommended to use Let's encrypt. Your own certificate is good for corporate networks, when website is not exposed on Internet._

_Note2: You probably don't need to generate certificate, if You deploy Your web application on one of hostings, that already offer HTTPS. List of those hosting providers can be found on [certbot hosting providers](https://certbot.eff.org/hosting_providers) website. In that case, You can skip to last step._

Then, You set cert and key properties in `svelte.config.js` in `config.kit.vite.server.https`, like this:

```js
// svelte.config.js
import fs from 'fs';

import adapter_node from '@sveltejs/adapter-node';

export default {
	kit: {
		adapter: adapter_node(),
		vite: {
			server: {
				https: {
					cert: fs.readFileSync('./cert.crt'),
					key: fs.readFileSync('./key.key'),
				}
			}
		}
	}
};
```

The last step is adding `--https` parameter to `svelte-kit` commands in `package.json` scripts.

```json
{
	"scripts": {
		"start": "node ./build --https",
		"dev": "svelte-kit dev --https",
		"build": "svelte-kit build",
		"preview": "svelte-kit preview --https",
	}
}
```

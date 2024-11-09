<h1 align="center"> <code>🔥hono-geo-middleware🔥</code> </h1>

geo information extractor middleware for [Hono](https://hono.dev/). Use to
extract geo information from request's header or serverless runtime context.

## Demo
live demo [here](https://hono-geo-middleware.vercel.app)


## How It Works

most serverless runtime provide useful information in request header or platform specific context api.
this middleware extract some common geo information and other useful info (maybe 😜) from
request header and ctx object.

- cloudflare worker: [cf properties](https://developers.cloudflare.com/workers/runtime-apis/request/#incomingrequestcfproperties)

- cloudflare cdn: [cf-common-header](https://developers.cloudflare.com/fundamentals/reference/http-request-headers/#cf-ipcountry) and [visitor-location-header](https://developers.cloudflare.com/rules/transform/managed-transforms/reference/#add-visitor-location-headers)

- netlify: edge functions specific [context object](https://docs.netlify.com/edge-functions/api/#netlify-specific-context-object)

- vercel: [geo header](https://vercel.com/guides/geo-ip-headers-geolocation-vercel-functions) and [more](https://github.com/vercel/vercel/blob/main/packages/functions/src/headers.ts)

- aws cloudfront(lambda@edge): [viewer location header](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/adding-cloudfront-headers.html#cloudfront-headers-viewer-location)


## Usage

```ts
import {getGeo, GeoMiddleware} from "hono-geo-middleware";
const app = new Hono()
app.use('/*', GeoMiddleware())
app.get('/geo', (c) => c.json(getGeo(c)))

export default {
  fetch: app.fetch
}
```


#### With Custom Extractor
```ts

import {getGeo, GeoMiddleware} from "hono-geo-middleware";
const app = new Hono()
const customExtractor = (c:Context) => {
  // extract from request
  const ip = c.req.header('x-real-ip')
  return {
    ip
  }
}
app.use('/*', GeoMiddleware({
  extractors: [customExtractor, 'vercel', 'cloudflare', 'cloudflare-worker'],
}))

app.get('/geo', (c) => c.json(getGeo(c)))

export default {
  fetch: app.fetch
}
```

## interface
```ts
const example = {
  "ip": "1.1.1.1",
  "city": "Tokyo",
  "country": "Japan",
  "countryCode": "JP",
  "continent": "AS",
  "latitude": "123.5670",
  "longitude": "456.7890",
  "timezone": "Asia/Tokyo",
  "postalCode": "151-0000",
  "flag": "🇯🇵",
}
```

```ts
interface Geo {
  city?: string;
  country?: string;
  /* ISO 3166-2 code */
  countryCode?: string;
  // country region name
  region?: string;
  /* ISO 3166-2 code */
  regionCode?: string;
  latitude?: string;
  longitude?: string;
  continent?: string;
  postalCode?: string;
  metroCode?: string;
  // client
  timezone?: string;
  asn?: string;
  idcRegion?: string;
  /** flag emoji */
  flag?: string;
}


```

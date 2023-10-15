# Hashnode Blog Starter Kit

Starter Kit built with Hashnode Public APIs. Use Hashnode's [world class editor](https://hashnode.com/neptune) to author content and collaborate. Use our starter kit to customize the front end.

## How to deploy

### Step 1

The recommended approach is depoying to Vercel. If you don't have an account already, sign up for a free plan.

- Fork this repo
- Create a new project on Vercel and connect this repo
- It's a monorepo. So, choose the following folder as Root Directory while importing on Vercel: `packages/blog-starter-kit/themes/enterprise`
  ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1695083263935/T5bByLxZT.png?auto=format)
- **Make sure to choose `Next.js` as framework preset (just above Root Directory setting). It appears to get reset after selecting Root Directory. So, double check this.**
- Set the following env vars

```
NEXT_PUBLIC_HASHNODE_GQL_ENDPOINT=https://gql.hashnode.com
NEXT_PUBLIC_HASHNODE_PUBLICATION_HOST=engineering.hashnode.com -> Change this to your Hashnode blog URL i.e. handle.hashnode.dev
NEXT_PUBLIC_BASE_URL=/blog -> This could be /blog if you are installing on subpath; otherwise don't set it
NEXT_PUBLIC_MODE=production
```

Once this is deployed, just visit Vercel's auto generated domain to ensure it loads fine. Initially you won't see any posts. But you can always point `NEXT_PUBLIC_HASHNODE_PUBLICATION_HOST` to a different domain such as `engineering.hashnode.com` to visualize.

### Step 2 (subpath installation)

#### Vercel

If your main project is deployed on Vercel, add the following rewrite to `next.config.js`:

```
async rewrites() {
    return [
      {
        source: "/blog",
        destination: "https://starter-kit-rose-seven.vercel.app/blog", -> Replace https://starter-kit-rose-seven.vercel.app with your own Vercel deployment URL from step 1
      },
      {
        source: "/blog/:path*",
        destination: "https://starter-kit-rose-seven.vercel.app/blog/:path*", -> Replace https://starter-kit-rose-seven.vercel.app with your own Vercel deployment URL from step 1
      },
    ];
  },
```

Once you deploy your project, the subpath installation should work successfully.

#### Cloudflare

In case you are using Cloudflare in proxy mode (orange cloud on), you can deploy the following worker script and map it to `yourdomain.com/*`:

```
const subpath = '/blog'; // Replace with your subpath
const blogBaseUrl = 'https://blog-woad-six-17.vercel.app'; // Replace with your blog URL from step 1

addEventListener('fetch', event => {
  event.respondWith(handleRequest(event.request))
})

/**
 * Respond to the request
 * @param {Request} request
 */
async function handleRequest(request) {
  const url = new URL(request.url)

  if (url.pathname.startsWith(subpath)) {
    // Proxy blog requests
    return proxyBlog(request) 
  } else {
    // Passthrough everything else
    return fetch(request)
  }
}

/**
 * Proxy blog requests
 * @param {Request} request
 */
async function proxyBlog(request) {
  const path = new URL(request.url).pathname;
  return fetch(`${blogBaseUrl}${path}`, request)
}
```

Be sure to replace the values of `subpath` and `blogBaseUrl` in the above snippet. This way cloudflare will proxy all the requests starting with `youdomain.com/blog` to your headless blog, and other requests will hit your origin as usual.

If your main domain is hosted elsewhere, you need to involve engineers from your team to create above rewrites.

## Running Locally

- cd `packages/blog-starter-kit/themes/enterprise` or `packages/blog-starter-kit/themes/personal`
- Copy `.env.example` to `.env.local`
- `pnpm install`
- `pnpm dev`

Visit http://localhost:3000!

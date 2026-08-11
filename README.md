# beam

A tiny, **TypeScript-first** social share helper. A fork of [sharer.js](https://github.com/ellisonleao/sharer.js), rebuilt as one typed function with zero runtime dependencies and no bundled UI.

## why beam?

- **Zero UI, zero deps** — it's a single function that returns a share URL. No DOM parsing, no hidden markup, no CSS to override. Your design, your buttons.
- **TypeScript-first** — every network has its own typed options. Autocomplete tells you what each sharer accepts, and wrong option names are compile errors, not silent bugs.
- **49 networks** — X/Twitter, Facebook, Bluesky, Threads, WhatsApp, Telegram, Reddit, LinkedIn, Pinterest, email, SMS… and plenty of niche ones.
- **Works everywhere** — plain JS, Svelte, React, Vue, Solid, Qwik, or anything else that can call a function.
- **Small** — tree-shakeable ESM published as `dist` + `.d.ts`, with `sideEffects: false` so bundlers keep only what you use.
- **Your call on the window** — open a centered popup (default), navigate the current tab, or open a new tab. `onWindowClose` lets you react when the popup closes.

## usage

```ts
import { share } from 'beam';

// Opens a centered popup
share('https://example.com', 'x', {
  title: 'beam — a tiny social share helper',
});

// Navigate instead of popup
share('https://example.com', 'reddit', {
  title: 'Beam me up',
  link: true, // open in current tab
  // or link: true, blank: true → open in a new tab
});

// Per-network options
share('https://example.com', 'facebook', {
  url: 'https://example.com',
  quote: 'Custom quote for the share card',
  hashtag: 'beamjs', // gets a leading # added automatically
});

share('dev@example.com', 'email', {
  to: 'friend@example.com',
  subject: 'Check out beam',
  title: 'Beam is neat',
});
```

### API

```ts
share(url: string, sharer: SharerName, options?: ShareOptions): string | false
```

Returns the generated share URL (so you can do your own thing with it — `navigator.share`, copy-to-clipboard, a plain `<a href>`…).

Common options:

| option | type | description |
| --- | --- | --- |
| `title` | `string` | share text / message |
| `link` | `boolean` | navigate instead of popping up (default `false`) |
| `blank` | `boolean` | with `link: true`, open the URL in a new tab |
| `popup` | `{ width?, height? }` | custom popup size (default `600×480`) |
| `onWindowClose` | `() => void` | called when the (new-tab) window closes |

Every network additionally accepts its own options — e.g. `hashtags`, `via` and `related` on X/Twitter; `to` and `subject` on email/WhatsApp; `image` and `description` on Pinterest. The full list is enforced by the types, so let your editor complete them.

## simple usage

A single button, zero build step:

```html
<button id="share-x">Share on X</button>

<script type="module">
  import { share } from 'beam';

  document.querySelector('#share-x').addEventListener('click', () => {
    share('https://example.com', 'x', { title: 'Hello from beam' });
  });
</script>
```

Or, if a plain link is all you need, use the returned URL directly:

```html
<a id="share-link" href="#">Share this page</a>

<script type="module">
  import { share } from 'beam';

  const link = document.querySelector('#share-link');
  link.href = share(location.href, 'facebook', {
    title: document.title,
    link: true,
    blank: true,
  });
</script>
```

## Use your own icons / UI design

beam ships no icons or widgets — a share button is just a function call, so your markup and CSS stay yours. Works identically in any framework; here's a Svelte example.

```svelte
<!-- ShareButton.svelte -->
<script lang="ts">
  import { share } from 'beam';
  import type { SharerName } from 'beam';

  let {
    sharer,
    url,
    title,
  }: { sharer: SharerName; url: string; title: string } = $props();

  const handleShare = () => share(url, sharer, { title });
</script>

<button onclick={handleShare} class="beam-btn">
  <slot />
</button>

<style>
  .beam-btn {
    border: 0;
    border-radius: 8px;
    padding: 8px 12px;
    cursor: pointer;
  }
</style>
```

```svelte
<!-- Your page -->
<script lang="ts">
  import ShareButton from './ShareButton.svelte';

  const url = 'https://example.com';
</script>

<ShareButton {url} sharer="x" title="Check out beam">
  <svg aria-hidden="true" width="16" height="16"><!-- your X icon --></svg>
  Share on X
</ShareButton>
```

A whole bar is the same idea:

```svelte
<script lang="ts">
  import { share } from 'beam';
  import type { SharerName } from 'beam';

  const url = 'https://example.com';
  const bar: { sharer: SharerName; label: string }[] = [
    { sharer: 'x', label: 'X' },
    { sharer: 'facebook', label: 'Facebook' },
    { sharer: 'bluesky', label: 'Bluesky' },
    { sharer: 'whatsapp', label: 'WhatsApp' },
  ];
</script>

<div class="share-bar">
  {#each bar as { sharer, label }}
    <button onclick={() => share(url, sharer, { title: document.title })}>
      {label}
    </button>
  {/each}
</div>
```

## Related projects

- [sharer.js](https://github.com/ellisonleao/sharer.js) — the original attribute-driven library beam is forked from
- [shareon](https://github.com/NickKaramoff/shareon) — another tiny, dependency-free share helper with bundled CSS
- [react-share](https://github.com/nygardk/react-share) — React component library with ready-made share buttons
# Plugins and Extensions

Use for adding Starlight plugins, evaluating community extensions, and authoring plugin code.

Source docs: `resources/plugins.mdx`, `reference/plugins.md`.

## Use Existing Plugins

Starlight plugins customize configuration, UI, and behavior.

```js
import starlight from '@astrojs/starlight';
import somePlugin from 'some-starlight-plugin';

export default defineConfig({
  integrations: [
    starlight({
      title: 'Docs',
      plugins: [somePlugin()],
    }),
  ],
});
```

Before adding a plugin:

1. Check `package.json` and lockfile for Astro/Starlight versions.
2. Read the plugin README/current docs.
3. Install with the local package manager.
4. Add the plugin in `starlight({ plugins: [...] })`.
5. Run build and inspect routes/sidebar/search/UI.

## Plugin Catalog Categories

Official:

- Algolia DocSearch: alternate search provider.

Community examples from the Starlight docs resources page:

- `starlight-links-validator`: broken links.
- `starlight-typedoc`: TypeScript API docs from TypeDoc.
- `starlight-blog`: blog in docs.
- `starlight-openapi`: OpenAPI/Swagger docs.
- `starlight-obsidian`: publish Obsidian vaults.
- `starlight-image-zoom`: image zoom.
- `starlight-versions`: versioned docs.
- `starlight-llms-txt`: generate `llms.txt`.
- Sidebar, TOC, draft, tags, announcement, copy button, comments, video, keyboard, and UI tweak plugins.

Treat every community plugin API as version-sensitive.

## Plugin API Shape

```ts
interface StarlightPlugin {
  name: string;
  hooks: {
    'i18n:setup'?: (options: {
      injectTranslations: (
        translations: Record<string, Record<string, string>>
      ) => void;
    }) => void | Promise<void>;
    'config:setup': (options: {
      config: StarlightUserConfig;
      updateConfig: (newConfig: StarlightUserConfig) => void;
      addIntegration: (integration: AstroIntegration) => void;
      addRouteMiddleware: (config: {
        entrypoint: string;
        order?: 'pre' | 'post' | 'default';
      }) => void;
      astroConfig: AstroConfig;
      command: 'dev' | 'build' | 'preview';
      isRestart: boolean;
      logger: AstroIntegrationLogger;
      useTranslations: (lang: string) => I18nT;
      absolutePathToLang: (path: string) => string;
    }) => void | Promise<void>;
  };
}
```

Verify exact types against `@astrojs/starlight/types` for installed version.

## `i18n:setup`

Inject plugin UI strings:

```ts
export default {
  name: 'plugin-with-translations',
  hooks: {
    'i18n:setup'({ injectTranslations }) {
      injectTranslations({
        en: { 'myPlugin.doThing': 'Do the thing' },
        fr: { 'myPlugin.doThing': 'Do the thing in French' },
      });
    },
  },
};
```

For TypeScript typing in plugin code, declare Starlight locals and extend `StarlightApp.I18n` according to current docs.

## `config:setup`

Read config and update it:

```ts
export default {
  name: 'add-social-plugin',
  hooks: {
    'config:setup'({ config, updateConfig }) {
      updateConfig({
        social: [
          ...config.social,
          { icon: 'github', label: 'GitHub', href: 'https://github.com/example/project' },
        ],
      });
    },
  },
};
```

Nested config must be supplied as a whole object. Spread existing nested values when extending.

Add Astro integrations:

```ts
import react from '@astrojs/react';

export default {
  name: 'plugin-using-react',
  hooks: {
    'config:setup'({ addIntegration, astroConfig }) {
      const hasReact = astroConfig.integrations.some(({ name }) => name === '@astrojs/react');
      if (!hasReact) addIntegration(react());
    },
  },
};
```

Add Starlight route middleware:

```ts
export default {
  name: 'plugin-with-middleware',
  hooks: {
    'config:setup'({ addRouteMiddleware }) {
      addRouteMiddleware({ entrypoint: './src/plugin-middleware.ts', order: 'pre' });
    },
  },
};
```

## Review Checklist

- Plugin package and options verified against current docs.
- Compatibility checked with installed Astro/Starlight.
- No secrets committed.
- Generated routes/content are included in sidebar and search intentionally.
- Build passes.
- i18n and base path behavior checked when relevant.

# Kunkun Ask AI extension

A simple extension to ask gemini api questions.

Minimal, but works for my use cases.

More information about [KunKun](https://github.com/kunkunsh/kunkun).

## Development

Development is the same as developing a normal website.

```bash
pnpm install
pnpm dev
pnpm build
```

- To develop and preview the extension in Kunkun, you need to run the `Add Dev Extension` command in Kunkun, and register this extension's path.

In `package.json`, `"devMain"` is the url for development server, and `"main"` is the path to static `.html` file for production.

To load the extension in development mode, you have to enable it with `Toggle Dev Extension Live Load Mode` command in Kunkun. A `Live` badge will be shown on the commands. This indicates that dev extensions will be loaded from `devMain` instead of `main`.

## Advanced


## Verify Build and Publish

```bash
pnpm build # make sure the build npm script works
npx kksh@latest verify # Verify some basic settings
npx kksh@latest verify --publish # Verify some basic settings before publishing
```

See [Documentation](https://docs.kunkun.sh/guides/extensions/publish/design/) for more details on how to publish your extension. You will need to publish your extension package to npm or jsr first with GitHub actioin, then register it on Kunkun's website.

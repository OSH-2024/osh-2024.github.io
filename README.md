# USTC OSH 2024 course homepage

See <https://osh-2024.github.io/>

## Development

First install dependencies and start server:

```bash
pip install -r requirements.txt
mkdocs serve
```

Then make your modifications.

Finally commit your changes. **Make sure files are formatted!**

## format

In order to keep the doc style consistent, we use [Prettier](https://prettier.io/) and [AutoCorrect](https://github.com/huacnlee/autocorrect).

```bash
# you need Node.js installed first
npm install # then install Prettier and AutoCorrect
```

See [Prettier Doc: Editor Integration](https://prettier.io/docs/en/editors.html) and [AutoCorrect: VS Code Extension](https://github.com/huacnlee/autocorrect#vs-code-extension) for editor intergration.

You should **format markdown files before commit**:

```bash
# see .prettierignore for ignoring certain files or folders
npm run test    # to see if there is a format error
npm run format  # to format all files under docs/
```

## License

Some code is explicitly licensed under Apache 2.0.
Other code without explicit notice is All Rights Reserved by default.

All texts are All Rights Reserved by default.

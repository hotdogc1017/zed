# Tailwind CSS

Zed 原生支持 Tailwind CSS 的自动补全、Lint 与悬停预览。

- 语言服务器：[tailwindlabs/tailwindcss-intellisense](https://github.com/tailwindlabs/tailwindcss-intellisense)

## 配置

可参考 [Tailwind CSS 语言服务器设置](https://github.com/tailwindlabs/tailwindcss-intellisense?tab=readme-ov-file#extension-settings)，并在 `settings.json` 的 `lsp` 段中配置：

```json [settings]
{
  "lsp": {
    "tailwindcss-language-server": {
      "settings": {
        "classFunctions": ["cva", "cx"],
        "experimental": {
          "classRegex": ["[cls|className]\\s\\:\\=\\s\"([^\"]*)"]
        }
      }
    }
  }
}
```

Zed 中支持 Tailwind CSS 的语言包括：

- [Astro](./astro.md)
- [CSS](./css.md)
- [ERB](./ruby.md)
- [HEEx](./elixir.md#heex)
- [HTML](./html.md)
- [TypeScript](./typescript.md)
- [JavaScript](./javascript.md)
- [PHP](./php.md)
- [Svelte](./svelte.md)
- [Vue](./vue.md)

### Prettier 插件

Zed 原生支持 Prettier。如果安装了 [Tailwind CSS Prettier 插件](https://github.com/tailwindlabs/prettier-plugin-tailwindcss)，只需在 Prettier 配置中添加即可生效：

```json [settings]
// .prettierrc
{
  "plugins": ["prettier-plugin-tailwindcss"]
}
```

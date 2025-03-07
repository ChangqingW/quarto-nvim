# quarto-nvim

Quarto-nvim provides tools for working on [Quarto](https://quarto.org/) manuscripts in Neovim.
You can get started with Quarto [here](https://quarto.org/docs/get-started/).

## Walkthrough

The [get started section](https://quarto.org/docs/get-started/hello/neovim.html) also comes with a video version to walk you through.
The playlist is extened as more features are added, so join us for a "Coffee with Quarto and Neovim":

<https://youtu.be/3sj7clNowlA?list=PLabWm-zCaD1axcMGvf7wFxJz8FZmyHSJ7>

## Setup

You can install `quarto-nvim` from GitHub with your favourite Neovim plugin manager
like [lazy.nvim](https://github.com/folke/lazy.nvim), [packer.nvim](https://github.com/wbthomason/packer.nvim) or [VimPlug](https://github.com/junegunn/vim-plug).

Because Quarto provides a lot of functionality through integration with existing plugins,
some of those have to be told about the existence of `quarto-nvim` (like e.g. registering
it as a source for the autocompletion plugin `nvim-cmp`).

As such, we recommend you to experiment with the [quarto-nvim kickstarter configuration](https://github.com/jmbuhr/quarto-nvim-kickstarter)
and then pick the relevant parts from the
[`lua/plugins/quarto.lua`](https://github.com/jmbuhr/quarto-nvim-kickstarter/blob/main/lua/plugins/quarto.lua) file
to integrate it into your own existing configuration.

There is also a smaller configuration for slotting into your existing `lazy.nvim` (e.g. [LazyVim](https://www.lazyvim.org/)) configuration at
<https://github.com/jmbuhr/lazyvim-starter-for-quarto/blob/main/lua/plugins/quarto.lua>

Plugins and their configuration to look out for in either of those files are:

```lua
{
    'quarto-dev/quarto-nvim',
    'jmbuhr/otter.nvim',
    'hrsh7th/nvim-cmp',
    'neovim/nvim-lspconfig',
    'nvim-treesitter/nvim-treesitter'
}
```

Quarto-nvim requires Neovim >= **v0.9.0** (<https://github.com/neovim/neovim/releases/tag/stable>).
If you are unable to update Neovim, you can specify a specific version of the plugins
involved instead of the latest stable version.
How you do this will vary depending on your plugin manager, but you can see one example using `lazy.nvim` here:
<https://github.com/jmbuhr/quarto-nvim-kickstarter/blob/nvim-0.8.3/lua/plugins/quarto.lua>

The `version = ...` lines to look out for are for the following plugins:

```lua
{
    'quarto-dev/quarto-nvim',
    'jmbuhr/otter.nvim',
    'nvim-treesitter/nvim-treesitter'
}
```

## Usage

### Configure

You can pass a lua table with options to the setup function
as shown in [quarto-nvim-kickstarter/..quarto.lua](https://github.com/jmbuhr/quarto-nvim-kickstarter/blob/main/lua/plugins/quarto.lua)

It will be merged with the default options, which are shown below in the example.
If you want to use the defaults, simply call `setup` without arguments or with an empty table.

```lua
require'quarto'.setup{
  debug = false,
  closePreviewOnExit = true,
  lspFeatures = {
    enabled = true,
    languages = { 'r', 'python', 'julia', 'bash' },
    chunks = 'curly', -- 'curly' or 'all'
    diagnostics = {
      enabled = true,
      triggers = { "BufWritePost" }
    },
    completion = {
      enabled = true,
    },
  },
  keymap = {
    hover = 'K',
    definition = 'gd',
    rename = '<leader>lR',
    references = 'gr',
  }
}
```

### Preview

Use the command

```vim
QuartoPreview
```

or access the function from lua, e.g. to create a keybinding:

```lua
local quarto = require'quarto'
quarto.setup()
vim.keymap.set('n', '<leader>qp', quarto.quartoPreview, {silent = true, noremap = true})
```

Then use the keyboard shortcut to open `quarto preview` for the current file or project in the active working directory in the neovim integrated terminal in a new tab.

Note: While you can use `QuartoPreview` without configuring the plugin via `quarto.setup`,
other features strictly require it.

## Language support

### Demo

https://user-images.githubusercontent.com/17450586/209436101-4dd560f4-c876-4dbc-a0f4-b3a2cbff0748.mp4

### Usage

With the language features enabled, you can open the hover documentation
for R, python and julia code chunks with `K` (or configure a different shortcut).
You can got-to-definition with `gd`.

### Autocompletion

`quarto-nvim` now comes with a completion source for [nvim-cmp](https://github.com/hrsh7th/nvim-cmp) to deliver swift autocompletion for code in quarto code chunks.
With the quarto language features enabled, you can add the source in your `cmp` configuration:

```lua
-- ...
  sources = {
    { name = 'otter' },
  }
-- ...
```

### R diagnostics configuration

To make diagnostics work with R you have to configure the linter a bit, since the language
buffers in the background separate code with blank links, which we want to ignore.
Otherwise you get a lot more diagnostics than you probably want.
Add file `.lintr` to your home folder and fill it with:

```
linters: linters_with_defaults(
    trailing_blank_lines_linter = NULL,
    trailing_whitespace_linter = NULL
  )
```

You can now also enable other lsp features, such as the show hover function
and shortcut, independent of showing diagnostics by enabling lsp features
but not enabling diagnostics.

### Other edgecases

Other languages might have similar issues (e.g. I see a lot of warnings about whitespace when activating diagnostics with `lua`).
If you come across them and have a fix, I will be very happy about a pull request!
Or, what might ultimately be the cleaner way of documenting language specific issues, an entry in the [wiki](https://github.com/quarto-dev/quarto-nvim/wiki).

## Available Commnds

```vim
QuartoPreview
QuartoClosePreview
QuartoHelp <..>
QuartoActivate
QuartoDiagnostics
QuartoHover
QuartoSendAbove
QuartoSendAll
```

## Recommended Plugins

Quarto works great with a number of existing plugins in the neovim ecosystem.
You can find semi-opinionated but still minimal
configurations for `nvim` and `tmux`,
for use with Quarto, R and python in these two repositories:

- <https://github.com/jmbuhr/quarto-nvim-kickstarter>
- <https://github.com/jmbuhr/tmux-kickstarter>


---
title: "My LunarVim Config: 2023 Edition"
seoDescription: "Learn about my LunarVim config! Discover some plugins, overrides, and tips you can use for your own NeoVim config and dotfiles."
datePublished: Sat Jun 17 2023 14:00:39 GMT+0000 (Coordinated Universal Time)
cuid: clj02gi1l009kl4nv3yzfhu9t
slug: my-lunarvim-config-2023-edition
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1686971239534/86560bb8-e340-4c65-b8b2-d6ebd94cc64e.jpeg
tags: vim, neovim, editors, text-editors, configuration

---

I've been keeping my dotfiles private for some time now because I don't want to remove secret information and create a PR for every change. Now the config climbs out of its "private repository" cave for you to see in all its glory. Who's been requesting it? Most comments on my [YouTube channel](https://www.youtube.com/playlist?list=PL3ZaFz0_yUh7DuP5FoNBG-3CLnoG4nqsh).

**TL;DR:** Here's the link to the [full gist](https://gist.github.com/raheelrjunaid/7c72aa4a2b7ff264abee086bef81fd34#file-config-lua)

## What is LunarVim?

> An IDE layer for [Neovim](https://neovim.io/) with sane defaults. Completely free and community driven.
> 
> — [LunarVim Website](https://www.lunarvim.org/) as of June 16th, 2023

Tha— That's it.

---

## Overrides

### LunarVim

```javascript
lvim.log.level = "warn" 
lvim.leader = "space"
lvim.colorscheme = "no-clown-fiesta" // <-- Sucks Ass 🍑!
lvim.builtin.cmp.experimental.ghost_text = false
lvim.keys.normal_mode["<C-s>"] = ":w<cr>"
lvim.keys.normal_mode["<S-h>"] = ":BufferLineCyclePrev<CR>"
lvim.keys.normal_mode["<S-l>"] = ":BufferLineCycleNext<CR>"
lvim.builtin.alpha.active = true
lvim.builtin.alpha.mode = "dashboard"
lvim.builtin.terminal.active = true // What does this even do?
lvim.builtin.nvimtree.setup.view.side = "left"
lvim.builtin.nvimtree.setup.renderer.icons.show.git = false
lvim.format_on_save = true
lvim.lsp.null_ls.setup.debug = true // Because prettier can be slow
```

* My leader key is space because as a piano player and human, I'm not a fan of using my pinky
    
* My colour scheme/syntax highlighting theme is [No Clown Fiesta](https://github.com/no-clown-fiesta/no-clown-fiesta.nvim)
    
    * It's okay. I prefer monochrome, but I haven't found a theme that works well with lining warnings/errors, so this is what I've got ¯\\\_(ツ)\_/¯
        
* I've turned off the ghost text introduced in LunarVim's latest version. It overlaps with GitHub Copilot 😡
    
* [Nvim-tree](https://github.com/nvim-tree/nvim-tree.lua) comes installed with LunarVim. It's a file explorer that I've moved to the side and added icons to.
    

### Vim

```javascript
vim.opt.relativenumber = true -- set relative numbered lines
vim.g.copilot_no_tab_map = true
vim.g.copilot_assume_mapped = true
vim.api.nvim_set_keymap("i", "<C-l>", 'copilot#Accept("")',
                        {expr = true, silent = true})
vim.keymap.set("i", "jk", "<Esc>")
vim.opt.timeoutlen = 500
```

* Relative line numbers are used less these days with the [leap](https://github.com/ggandor/leap.nvim) plugin (see the plugins section for details)
    
* I do `Ctrl+L` for accepting Copilot suggestions since my hand is already there
    
* You best believe I ain't using my pinky to escape from insert mode
    

## LSP

### Treesitter

```javascript
lvim.builtin.treesitter.ensure_installed = {
    "bash", "c", "javascript", "json", "lua", "python", "typescript", "tsx",
    "css", "rust", "java", "yaml", "go", "svelte"
}
lvim.builtin.treesitter.highlight.enabled = true
```

Treesitter is for syntax highlighting, and I've got the basics with some exceptions I rarely use (like Terraform for example).

### Formatters/Linters

```javascript
require("lvim.lsp.null-ls.formatters").setup({
    {
        name = "prettierd",
        filetypes = {
        //   ...
        }
    }, {name = "prismaFmt"}, {name = "lua_format"}
})

require("lvim.lsp.null-ls.linters").setup {
    {
        name = "eslint_d",
        filetypes = {
        //   ...
        }
    }
}

// These aren't handled correctly, so I manually mount them for now
require("lvim.lsp.manager").setup("tailwindcss", opts)
require("lvim.lsp.manager").setup("golangci_lint_ls", opts)
require("lvim.lsp.manager").setup("eslint_d", opts)
```

I opt for the daemon versions of [eslint](https://github.com/mantoni/eslint_d.js/) and [prettier](https://github.com/fsouza/prettierd) because they save memory on my machine. I still hate it when they spawn multiple node processes; `killall node` to the rescue!

## Plugins

Here is the full list of plugins:

1. [**copilot.vim**](https://github.com/github/copilot.vim): Already explained this one in the LunarVim overrides; pay attention please ;)
    
2. [**vim-repeat**](https://github.com/tpope/vim-repeat): Allows the repeat command (`.`) to work with various Vim commands and plugins.
    
3. [**go.nvim**](https://github.com/ray-x/go.nvim): Provides enhanced Go language support for Vim, including syntax highlighting, code execution, debugging, etc.
    
4. [**vim-go**](https://github.com/fatih/vim-go): Provides comprehensive Go language support for Vim, including syntax checking, formatting, and more.
    
5. [**leap.nvim**](https://github.com/ggandor/leap.nvim): Enables jumping to lines based on visual hints and patterns within the current buffer.
    
6. [**symbols-outline.nvim**](https://github.com/simrat39/symbols-outline.nvim): Generates an outline of symbols (functions, classes, variables) in the current file for easy navigation.
    
7. [**todo-comments.nvim**](https://github.com/folke/todo-comments.nvim): Highlights and provides shortcuts for managing TODO/WARNING/INFO comments in your code.
    
8. [**vim-surround**](https://github.com/tpope/vim-surround): Simplifies surrounding text objects (words, lines, etc.) with parentheses, quotes, tags, and more.
    
9. [**nvim-ts-autotag**](https://github.com/windwp/nvim-ts-autotag): Automatically closes and updates HTML and XML tags in real-time as you type. For some reason, this doesn't work with Astro :(
    
10. [**nvim-ts-rainbow**](https://github.com/p00f/nvim-ts-rainbow): Colorizes parentheses, brackets, and braces to improve readability in nested code blocks.
    
11. [**onedarkpro.nvim**](https://github.com/olimorris/onedarkpro.nvim): My old theme, it looked like a clown barfed all over my screen so I got:
    
12. [**no-clown-fiesta.nvim**](https://github.com/aktersnurra/no-clown-fiesta.nvim): Removes excessive syntax highlighting and distractions from your code.
    
13. [**persistence.nvim**](https://github.com/folke/persistence.nvim): Saves and restores your Vim session, including open files, window layouts, and more.
    
14. [**vim-wakatime**](https://github.com/wakatime/vim-wakatime): Integrates with the WakaTime service to track and analyze your coding activity and productivity. I don't care, but I still use it because why not?
    
15. [**vim-terraform**](https://github.com/hashivim/vim-terraform): Provides syntax highlighting, indentation, and other features for working with Terraform configuration files. Terraform sucks.
    

## Wrapping it up

I'm not sure why people were so interested in my setup. Here's my step-by-step guide:

1. Install a config so the basics are out of the way (LSP, highlighting, organization, community, etc.)
    
2. Use a language
    
3. Find out it's missing a feature (formatting, linking, debugging, highlighting, etc.)
    
4. Add an override or plugin to add that feature
---
layout: post
title: Markdown Mode Memory
description: Creating a Visual Studio Code extension for remembering Markdown editor
  mode
era: current
permalink: "/posts/markdown-mode-memory/"
date: '2026-08-22 21:41:10 -0700'
---
Before the days of agentic coding, I was happy to use the text editor for Markdown in Visual Studio Code, and then occasionally run `Markdown: Open Preview to the Side`. Working with coding agents has changed that. When it was coworkers writing Markdown docs, I would just be reading them on GitHub in the browser, usually in a pull request. Now I often need to read the Markdown plans that Codex or Claude or Copilot have written, and I want to do that locally.

You can use `Cmd+Shift+V` to quickly switch to Markdown preview mode, but if I want to navigate between several Markdown files, I'd have to do that on opening each file. I *could* change my default to be preview mode, but when I'm the one writing the Markdown, I'd just be back in the same boat needing to run the keyboard shortcut each time. I could also use the new (experimental) `Markdown Editor` as my default, which gives a hybrid edit/preview experience, similar to Obsidian or other Markdown editors. But that's not really what I want either.

What I want is to be able to toggle between being in edit mode or view mode. Rather than introduce a new mode concept, what makes sense to me is if the system remembered when I switched from text editor to Markdown preview or vice versa, and then opened new Markdown files with whichever editor I last switched to.

First I tried both Google and ChatGPT to see if there was a way to already do this. While they each told me about extensions that would support automatically opening the preview to the side mode, it seemed there was no built-in way or existing extension to get the functionality I wanted. Without agentic coding tools, I might have stopped there; I could just change my default and get used to toggling as needed. With Codex, however, creating an extension just for this little thing didn't seem like such a bad idea anymore.

I started with a simple description of what I wanted:

> In Visual Studio Code, you can open a Markdown file in normal text mode or in Markdown preview mode, and as of a recent release, in hybrid editor (experimental). In text editor or markdown preview, I can hit cmd+shift+v to toggle to the other mode. And I can set a user preference to change the default for opening a file to be preview mode instead of text editor mode. But what I want is, whichever mode I toggle to (or if I change it to hybrid mode through other commands or UI), that the next markdown file I open use whichever mode I was using last. So I want to build a VS Code extension to do that.

It quickly built the structure for a VS Code extension and implemented a first attempt. That first try was built on the idea of *after* you open the editor, it would check a state value and then replace the tab with one opened in the desired editor. This led to a somewhat jarring experience of it seeming like it was flashing as it opened in one mode and then quickly changed to another. I suggested that it would be simpler and a better experience to update the default view settings and then let that drive how new files are opened. It had a firm opinion that silently updating user or workspace settings was a bad idea, and proposed keeping the current behavior as the default and letting the user opt in to changing their editor association setting. I pushed back that having two modes for this simple extension wasn't worthwhile, in particular because I'd be trying to support a mode that I might never use myself.

It proposed a plan that it thought would work more reliably than the first try, but still involve replacing the tab after open:

```text
You’re right. I would not ship two modes.
The cleaner design is one mechanism: keep the preference private to the workspace and always enforce it by reopening the active tab. Do not edit workbench.editorAssociations at all.
The prototype only needs one improvement: apply the remembered mode in both directions.
- Remembered Preview/hybrid + new source tab → reopen it as Preview/hybrid.
- Remembered source + a user association opens a new Preview/hybrid tab → reopen it as the default text editor.
- A same-file transition—Preview → source, source → hybrid, etc.—is recognized as an intentional user choice and becomes the new remembered mode.
Store that preference in workspaceState, rather than globalState, so each project naturally remembers its own last Markdown mode without writing to .vscode/settings.json, changing Git-tracked files, or adding product concepts.
The tradeoff is the brief reopen flicker. I think that’s much more acceptable than silently changing workspace configuration—and it keeps the extension’s story wonderfully simple: “Markdown files open in the last Markdown mode you used in this workspace.”
```

I wanted to try the experience to decide, so I let it try to build it. It continued to be unreliable, sometimes attempting fixes that made things worse. I switched from the `Terra` model to `Sol`, and it was finally able to produce an implementation that *mostly* worked. At that point, I said:

> Ok, that does seem to work. Sometimes though I'm hitting cmd+shift+v and it's not toggling to the other mode, and it's hard to tell, but I think it might be flickering, indicating it's changing mode but then being reset back.
> This whole thing is feeling messy and not nearly as simple & safe as I'd hoped.

It then agreed and recommended giving up! However, now I insisted on starting over by using the editor association setting as the mechanism. I still had to go through several rounds of testing, reporting errors, suggesting simplifications for implementation, as well as suggesting changes to the user experience of how this is enabled or disabled in a workspace. But we finally had a working extension that satisfied my needs.

I *think* that I'm not the only person in the world who might like this behavior option, and it also seems like publishing an extension is the easiest way to have it installed for my daily use, so I also used Codex to help me publish it. That was mostly straightforward, except that there are changes to authentication options which are coming to `@vscode/vsce` but not yet fully published, so I needed to guide it on backing off to the best current option after reading the docs myself.

The end result is an extension that you can install yourself if you would also find this helpful when using Markdown in VS Code: [Markdown Mode Memory](https://marketplace.visualstudio.com/items?itemName=jrduncans.markdown-mode-memory). If you have feedback, you can file an issue in the [GitHub repo](https://github.com/jrduncans/vscode-markdown-mode-memory) or find me on social media.

It took from about 9:30 a.m. to 1 p.m. on a Sunday to go from my first prompt to completion. While I did review the code a little to see what it was trying to do and to be able to identify where complexity was coming from, for the most part I was able to produce this while knowing next-to-nothing about VS Code extensions. Despite the very simple requirements, it required a lot of guidance and input from me to build the right thing.

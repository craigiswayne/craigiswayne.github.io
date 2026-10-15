| Feature / Attribute                 | popover="auto"                                            | popover="hint"                                                                         | popover="manual"                                                | Modal <dialog> (showModal())                                        | Non-Modal <dialog> (show())                                 |
|-------------------------------------|-----------------------------------------------------------|----------------------------------------------------------------------------------------|-----------------------------------------------------------------|---------------------------------------------------------------------|-------------------------------------------------------------|
| Primary Purpose                     | Ephemeral popups (menus, select dropdowns, context cards) | Subordinate hovercards, tooltips, and info previews                                    | Persistent floating UI (toasts, audio players, sticky toolbars) | Critical user interrupts, confirmations, forms, settings screens.   | Modeless utility panels (floating inspectors, chat widgets) |
| Top Layer Rendering?                | Yes (Promoted to top layer when open)                     | Yes (Promoted to top layer when open)                                                  | Yes (Promoted to top layer when open)                           | Yes (Promoted to top layer when open)                               | No (Stays in standard DOM z-index stack)                    |
| Focus Trapped?                      | No (Background remains interactive)                       | No (Background remains interactive)                                                    | No (Background remains interactive)                             | Yes (Traps tab focus; makes rest of document inert)                 | No (Background remains fully interactive)                   |
| Light Dismiss (Click Outside / Esc) | Yes (Clicking outside or pressing Esc closes it)          | Yes (Clicking outside or pressing Esc closes it)                                       | No (Must be closed programmatically or via trigger button)      | Partial (Esc key closes it; click-outside requires custom listener) | No (Esc key does not close it by default)                   |
| Stacking Dismissal Behavior         | Opening an `auto` popover closes sibling `auto` popovers. | Opening a `hint` popover closes other `hint` popovers, but leaves `auto` popovers open | Independent. Does not close or get closed by other popovers     | Stacks natively in top layer. Does not dismiss sibling modals       | Normal DOM z-index ordering rules                           |
| Backdrop Support                    | Yes (via ::backdrop, non-blocking)                        | Yes (via ::backdrop, non-blocking)                                                     | Yes (via ::backdrop, non-blocking)                              | Yes (via ::backdrop, blocks clicks to page)                         | No native ::backdrop                                        |                                                                                                                               


## Architectural Guidance & Common Pitfalls
* Do not use Modal Dialogs for simple menus or pickers:
  * Modal dialogs (showModal()) lock down the entire document and force the page into an inert state. 
  * If a user opens a profile dropdown or a calendar picker, they should still be able to scroll the page or click outside without breaking their context. Use popover="auto" for this.

* The popover="hint" advantage for tooltips:
  * Prior to popover="hint", if a user opened an auto dropdown menu and then hovered over an icon that triggered an auto tooltip, the tooltip opening would automatically force-close the dropdown menu. 
  * popover="hint" solves this by creating a separate, subordinate stacking layer. A hint popover can appear over an open auto menu without destroying the menu's state.

* Avoid using popover="manual" when you actually want a dialog:
  * Developers often choose popover="manual" because they want a dialog that stays open, but they end up having to manually reimplement accessibility hooks, ARIA attributes, and focus restoration. 
  * If your UI requires a persistent or modeless floating box (like a non-blocking rich text format bar or a dockable panel), use a non-modal <dialog> (dialog.show()) instead of abusing manual popovers.

Which specific UI pattern in your application are you building right now? I can help you pick the exact attribute and event setup for it.


```shell
    [Need an Overlay Component?]
                 │
                 ▼
┌─────────────────────────────────┐
│  Requires background blocking   │─── YES ──► [Modal Dialog]
│  & focus trap?                  │            (showModal())
└────────────────┬────────────────┘
                 │ NO
                 ▼
┌─────────────────────────────────┐
│  Is it a subordinate hover      │─── YES ──► [Popover Hint]
│  tooltip or hint?               │            (popover="hint")
└────────────────┬────────────────┘
                 │ NO
                 ▼
┌─────────────────────────────────┐
│  Is it an ephemeral menu or     │─── YES ──► [Popover Auto]
│  dropdown (dismisses outside)?  │            (popover="auto")
└────────────────┬────────────────┘
                 │ NO
                 ▼
┌─────────────────────────────────┐
│  Is it a modeless utility       │─── YES ──► [Non-Modal Dialog]
│  panel or workspace window?     │            (show())
└────────────────┬────────────────┘
                 │ NO
                 ▼
         [Popover Manual]
         (popover="manual")
```
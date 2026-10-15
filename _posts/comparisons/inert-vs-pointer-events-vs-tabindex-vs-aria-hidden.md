| Feature / Behavior          | pointer-events: none         | tabindex="-1'            | aria-hidden="true" | inert                           |
|-----------------------------|------------------------------|--------------------------|--------------------|---------------------------------|
| Type                        | CSS Property                 | HTML Attribute           | ARIA Attribute     | Global HTML Attribute           |
| Allows Clicks & Touch?      | No                           | Yes                      | Yes                | No                              |
| Allows Keyboard Focus?      | Yes                          | No (Element only)        | Yes                | No (Target + All Children)      |
| Allows Hover Interactions   | No                           | Yes                      | Yes                | No                              |
| Visible to Screen Readers?  | Yes                          | Yes                      | No                 | No                              |
| Cascades to Children?       | Yes (Unless child sets auto) | No (Target element only) | Yes                | Yes (Strict no child overrides) |
| User Can Still Select Text? | Yes (If highlightable)       | Yes                      | Yes                | No                              |

```mermaid
flowchart TD
    Start([Start: What do you need to disable or hide?]) --> Q1{Need to freeze an entire UI section?<br><i>Disable clicks, keyboard tabbing, AND screen readers</i>}

    Q1 -- Yes --> Inert["<b>use 'inert'</b><br>• Global HTML Attribute<br>• <i>Examples: Inactive slides, background content behind modals, closed drawers</i>"]

    Q1 -- No --> Q2{Need mouse/touch clicks to pass through a layer?}

    Q2 -- Yes --> PE["<b>use 'pointer-events: none'</b><br>• CSS Property<br>• <i>Examples: Decorative visual overlays, gradient masks, pass-through SVGs</i>"]

    Q2 -- No --> Q3{Hide decorative content from screen readers ONLY?}

    Q3 -- Yes --> AriaHidden["<b>use 'aria-hidden=true'</b><br>• ARIA Attribute<br>• <i>Examples: Decorative icons next to visible text, redundant badges</i>"]

    Q3 -- No --> Q4{Remove a SINGLE element from keyboard tab order?}

    Q4 -- Yes --> TabIndex["<b>use 'tabindex=-1'</b><br>• HTML Attribute<br>• <i>Examples: Managing focus programmatically via JavaScript</i>"]

    Q4 -- No --> Fallback["<b>Re-evaluate Requirement</b><br>• Use standard CSS <i>display: none</i> or <i>visibility: hidden</i> if hiding completely"]
```
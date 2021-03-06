---
id: dialogs
title: "Dialogs"
---

Playwright can interact with the web page dialogs such as [`alert`](https://developer.mozilla.org/en-US/docs/Web/API/Window/alert), [`confirm`](https://developer.mozilla.org/en-US/docs/Web/API/Window/confirm), [`prompt`](https://developer.mozilla.org/en-US/docs/Web/API/Window/prompt) as well as [`beforeunload`](https://developer.mozilla.org/en-US/docs/Web/API/Window/beforeunload_event) confirmation.

<!-- TOC -->

## alert(), confirm(), prompt() dialogs

You can register a dialog handler before the action that triggers the dialog to accept or decline it.

```js
page.on('dialog', dialog => dialog.accept());
await page.click('button');
```

```python async
page.on("dialog", lambda dialog: dialog.accept())
await page.click("button")
```

```python sync
page.on("dialog", lambda dialog: dialog.accept())
page.click("button")
```

:::note
If your action, be it [`method: Page.click`], [`method: Page.evaluate`] or any other, results in a dialog, the action will stall until the dialog is handled. That's because dialogs in Web are modal and block further page execution until they are handled.
:::

As a result, following snippet will never resolve:

:::warn
WRONG!
:::

```js
await page.click('button'); // Will hang here
page.on('dialog', dialog => dialog.accept())
```

:::warn
WRONG!
:::

```python async
await page.click("button") # Will hang here
page.on("dialog", lambda dialog: dialog.accept())
```

```python sync
page.click("button") # Will hang here
page.on("dialog", lambda dialog: dialog.accept())
```

### API reference

- [`Dialog`]
- [`method: Dialog.accept`]
- [`method: Dialog.dismiss`]

## beforeunload dialog

When [`method: Page.close`] is invoked with the truthy [`option: runBeforeUnload`] value, it page runs its unload handlers. This is the only case when [`method: Page.close`] does not wait for the page to actually close, because it might be that the page stays open in the end of the operation.

You can register a dialog handler to handle the beforeunload dialog yourself:

```js
page.on('dialog', async dialog => {
  assert(dialog.type() === 'beforeunload');
  await dialog.dismiss();
});
await page.close({runBeforeUnload: true});
```

```python async
async def handle_dialog(dialog):
    assert dialog.type == 'beforeunload'
    await dialog.dismiss()

page.on('dialog', lambda: handle_dialog)
await page.close(run_before_unload=True)
```

```python sync
def handle_dialog(dialog):
    assert dialog.type == 'beforeunload'
    dialog.dismiss()

page.on('dialog', lambda: handle_dialog)
page.close(run_before_unload=True)
```

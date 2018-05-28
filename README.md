# MoveTabTester

Test the webextensions tabs.move function

## Build instructions:

make

## Loading instructions:

Firefox: Open the pseudo-web page `about:debugging#addons`, press `Load Temporary Add-On`, and select `.../build/firefox/manifest.json`

Chrome: Open the pseudo-web page `chrome://extensions`, enable Developer Mode, press `Load Unpacked`, navigate to `.../build/` and select the `chrome` directory (not the manifest.json file).

## Running

In the top list, select the tab(s) you wish to move.

Pick the window you want to move them to in the first dropdown list.

In the second dropdown list, pick the tab destination: you want the source tabs to end up
to the *left* of this tab. To move the tabs to the end of the window, select the '[END]' 
tab at the bottom of the list. This entry corresponds to a tabIndex of -1 in the call to `tabs.move`.

There are four different ways to attempt to move the tabs:

1. "Naive All":
Takes the IDs for the tabs you selected (`T = [t1, t2, ..., tn]`) and calls `tabs.move(T, { windowId:wid, index: targetTabIndex})`

2. "Naive Individually"
Peels off one tab at a time (from the start targetting END, the end otherwise).  For selected tabs `T = [t1, t2, ..., tn]`, it sequentially calls
`tabs.move(tn, { windowId:wid, index: targetTabIndex)`
`tabs.move(t<n-1>, { windowId:wid, index: targetTabIndex)`
...
`tabs.move(t1, { windowId:wid, index: targetTabIndex)`

3. "Offset One All"
If all the selected tabs lie in the same window as the target tab and are to the left of that tab, it calls
`tabs.move(T, { windowId:wid, index: targetTabIndex - 1 })`
This framework deliberately gives an alert error and throws an exception if some tabs in the target's window lie to the left and some lie to the right and/or in other windows.

4. "Offset Individually"
This is the only one that works consistently in both firefox and chrome: 
Peels off one tab at a time (from the start targetting END, the end otherwise).  For selected tabs `T = [t1, t2, ..., tn]`, it sequentially calls
`tabs.move(tn, { windowId:wid, index: checkAndAdjust(tn, targetTabIndex))`
`tabs.move(t<n-1>, { windowId:wid, index: checkAndAdjust(t<n-1>, targetTabIndex))`
...
`tabs.move(t1, { windowId:wid, index: checkAndAdjust(t1, targetTabIndex))`

Option 4 is a lot more work to implement than option 1, but option 1 only works reliably when moving tabs backwards in the same window.

# Customize the Vscode theme in user setting


```json
{
    "editor.tokenColorCustomizations": {
    "[Default Light Modern]": {
      "textMateRules": [
        {
          "scope": [
        
            "entity.name",
            "entity.name.function",
            "support.function",
            "keyword",
            "storage",
            "punctuation"
          ],
          "settings": {
            "fontStyle": "bold"
          }
        }
      ]
    }
  },
  "workbench.colorCustomizations": {
    "[Default Light Modern]": {
      "editor.background": "#EBF6FF",
      "editor.foreground": "#102030",

      "editorLineNumber.foreground": "#7F9AAF",
      "editorCursor.foreground": "#000000",
      "editor.selectionBackground": "#B9E0FF",
      "editor.inactiveSelectionBackground": "#D7ECFC",
      "editor.lineHighlightBackground": "#DFF1FF",
      "editorWhitespace.foreground": "#B4CDDF",

      "tab.activeBackground": "#FFFFFF",
      "tab.activeForeground": "#102030",
      "tab.inactiveBackground": "#D7ECFC",
      "tab.inactiveForeground": "#5E7688",
      "tab.border": "#C2D9EA",

      "sideBar.background": "#E1F1FD",
      "sideBar.foreground": "#102030",
      "sideBarSectionHeader.background": "#D2E8F8",
      "sideBarSectionHeader.foreground": "#102030",

      "activityBar.background": "#D2E8F8",
      "activityBar.foreground": "#0B1F33",
      "activityBar.inactiveForeground": "#6D879A",
      "activityBarBadge.background": "#007ACC",
      "activityBarBadge.foreground": "#FFFFFF",

      "titleBar.activeBackground": "#E1F1FD",
      "titleBar.activeForeground": "#102030",
      "titleBar.inactiveBackground": "#F3FAFF",
      "titleBar.inactiveForeground": "#6D879A",

      "statusBar.background": "#D2E8F8",
      "statusBar.foreground": "#102030",
      "statusBar.noFolderBackground": "#D2E8F8",
      "statusBar.debuggingBackground": "#FFD966",
      "statusBar.debuggingForeground": "#000000",

      "panel.background": "#F5FBFF",
      "panel.border": "#C2D9EA",

      "terminal.background": "#EBF6FF",
      "terminal.foreground": "#102030",
      "terminalCursor.foreground": "#000000"
    },
    "[Default Dark+]": {
      "activityBar.activeBackground": "#4D2F00",
      "activityBar.activeBorder": "#FF9E00",
      "activityBar.background": "#111111",
      "activityBar.dropBorder": "#FF9E00",
      "activityBarBadge.background": "#FF9E00",
      "badge.background": "#FF9E00",
      "breadcrumb.activeSelectionForeground": "#FFB133",
      "breadcrumb.focusForeground": "#FFB133",
      "button.background": "#CC7E00",
      "button.hoverBackground": "#FF9E00",
      "editor.background": "#000000",
      "editor.findMatchBackground": "#663F00",
      "editor.findMatchBorder": "#FF9E00",
      "editor.findMatchHighlightBackground": "#704500",
      "editor.inactiveSelectionBackground": "#663F00",
      "editor.lineHighlightBackground": "#282828",
      "editor.selectionBackground": "#663F00",
      "editor.selectionHighlightBackground": "#663F00",
      "editor.selectionHighlightBorder": "#FF9E00",
      "editorBracketMatch.border": "#FF9E00",
      "editorCursor.foreground": "#FFB133",
      "editorInfo.foreground": "#FF9E00",
      "editorLineNumber.activeForeground": "#FFC466",
      "editorLink.activeForeground": "#FFC466",
      "editorSuggestWidget.highlightForeground": "#FF9E00",
      "editorWidget.border": "#FF9E00",
      "editorWidget.resizeBorder": "#FF9E00",
      "focusBorder": "#995F00",
      "list.activeSelectionBackground": "#663F00",
      "list.highlightForeground": "#FF9E00",
      "list.hoverBackground": "#4D2F00",
      "list.inactiveSelectionBackground": "#4D2F00",
      "menu.selectionBackground": "#4D2F00",
      "menu.selectionForeground": "#FFFFFF",
      "notificationLink.foreground": "#FF9E00",
      "panel.background": "#181818",
      "panelTitle.activeBorder": "#FF9E00",
      "pickerGroup.foreground": "#CC7E00",
      "progressBar.background": "#FF9E00",
      "scrollbarSlider.activeBackground": "#CC7E00",
      "searchEditor.findMatchBackground": "#804F00",
      "selection.background": "#663F00",
      "settings.focusedRowBorder": "#663F00",
      "settings.headerForeground": "#E68E00",
      "settings.modifiedItemIndicator": "#E68E00",
      "sideBar.background": "#181818",
      "sideBar.border": "#000000",
      "statusBar.background": "#CC7E00",
      "statusBar.foreground": "#FFFFFF",
      "statusBar.noFolderBackground": "#CC7E00",
      "tab.activeBorder": "#FF9E00",
      "tab.activeModifiedBorder": "#FF9E00",
      "terminal.selectionBackground": "#663F00",
      "terminalCursor.foreground": "#FFB133",
      "textLink.activeForeground": "#FFBB4D",
      "textLink.foreground": "#FF9E00",
      "window.activeBorder": "#995F00",
    },
  }
 }

 ```
--[[
=========================================================================================================================
Rayfield Interface Suite Documentation
=========================================================================================================================

Welcome to the documentation for the Rayfield Interface Suite. This guide will walk you through creating a powerful
and professional-looking user interface for your scripts.

-------------------------------------------------------------------------------------------------------------------------
-- // Section 1: Getting Started - Creating the Window
-------------------------------------------------------------------------------------------------------------------------

The first step is to initialize the library and create the main window. This is done with the `CreateWindow` function.

`RayfieldLibrary:CreateWindow(SettingsTable)`

This function takes a single argument: a table containing the settings for your window.

-- Arguments:
    `SettingsTable` (table): A dictionary of settings for the window.

-- SettingsTable Fields:
    `Name` (string): The title of your window, displayed in the top bar.
    `LoadingTitle` (string, optional): The main title shown during the loading screen. Defaults to "Rayfield Interface Suite".
    `LoadingSubtitle` (string, optional): The subtitle shown during the loading screen. Defaults to "by Sirius".
    `ConfigurationSaving` (table, optional): Settings for saving user configurations.
        `Enabled` (boolean): If true, user settings for toggles, sliders, etc., will be saved.
        `FileName` (string): The name of the file to save the configuration to. Defaults to the game's PlaceId.
    `KeySystem` (boolean, optional): Set to true to enable a key system before the UI loads. Requires `KeySettings`.
    `KeySettings` (table, optional): A table containing settings for the key system.
        `Key` (string or table): The key(s) required to access the UI.
        `Title` (string): The title displayed on the key input window.
        `Subtitle` (string): The subtitle displayed on the key input window.
        `Note` (string): Instructional text on the key input window.
        `SaveKey` (boolean): If true, the correct key will be saved for future sessions.

-- Returns:
    `WindowObject` (table): An object representing the created window, used to add tabs.

-- Example:
    local Library = loadstring(game:HttpGet("https://raw.githubusercontent.com/barbarianTopG/Rayfield-Old-Maintained/refs/heads/main/source"))()

    local Window = Library:CreateWindow({
        Name = "My Awesome Script",
        LoadingTitle = "My Awesome Script Hub",
        LoadingSubtitle = "by YourName",
        ConfigurationSaving = {
            Enabled = true,
            FileName = "MyAwesomeScriptConfig"
        }
    })

-------------------------------------------------------------------------------------------------------------------------
-- // Section 2: Building the UI - Tabs and Sections
-------------------------------------------------------------------------------------------------------------------------

Once you have a `WindowObject`, you can start adding tabs to it.

`WindowObject:CreateTab(Name, ImageId)`

-- Arguments:
    `Name` (string): The name of the tab to be displayed.
    `ImageId` (number, optional): A Roblox asset ID for an icon to display next to the tab name.

-- Returns:
    `TabObject` (table): An object representing the created tab, used to add elements.

-- Example:
    local MainTab = Window:CreateTab("Main", 4882435225)
    local VisualsTab = Window:CreateTab("Visuals", 5022510825)

-- You can also create sections within a tab to organize your elements.

`TabObject:CreateSection(Name)`

-- Arguments:
    `Name` (string): The name of the section header.

-- Returns:
    `SectionObject` (table): An object with a `:Set(newName)` method to dynamically change the section name.

-- Example:
    MainTab:CreateSection("Player Functions")

-------------------------------------------------------------------------------------------------------------------------
-- // Section 3: Populating Tabs - UI Elements
-------------------------------------------------------------------------------------------------------------------------

Each `TabObject` has methods to create various UI elements. All element creation functions return an object that allows
you to interact with or update the element later.

---
-- 3.1: Button
---
`TabObject:CreateButton(SettingsTable)`

-- SettingsTable Fields:
    `Name` (string): The text displayed on the button.
    `Callback` (function): The function to execute when the button is clicked.

-- Returns:
    `ButtonObject` with `:Set(newName)` method.

-- Example:
    MainTab:CreateButton({
        Name = "Infinite Jump",
        Callback = function()
            print("Infinite Jump button clicked!")
            -- Your code here
        end,
    })

---
-- 3.2: Toggle
---
`TabObject:CreateToggle(SettingsTable)`

-- SettingsTable Fields:
    `Name` (string): The label for the toggle.
    `CurrentValue` (boolean, optional): The default state of the toggle (false if omitted).
    `Flag` (string, optional): A unique identifier used for saving this toggle's state if `ConfigurationSaving` is enabled.
    `Callback` (function): The function to execute when the toggle state changes. It receives the new state as an argument (`true` or `false`).

-- Returns:
    `ToggleObject` with `:Set(newValue)` method.

-- Example:
    local FlyToggle = MainTab:CreateToggle({
        Name = "Fly",
        CurrentValue = false,
        Flag = "PlayerFly", -- Must be unique
        Callback = function(Value)
            print("Fly is now:", Value)
            -- Your fly script logic here
        end,
    })
    -- Later in your script:
    -- FlyToggle:Set(true) -- Programmatically enable the toggle

---
-- 3.3: Slider
---
`TabObject:CreateSlider(SettingsTable)`

-- SettingsTable Fields:
    `Name` (string): The label for the slider.
    `Range` (table): A table with two numbers `{min, max}` defining the slider's range.
    `Increment` (number, optional): The step value for the slider. Defaults to 1.
    `Suffix` (string, optional): Text to display after the number (e.g., "studs").
    `CurrentValue` (number, optional): The default value of the slider.
    `Flag` (string, optional): A unique identifier for saving.
    `Callback` (function): Called when the slider's value changes. Receives the new number value.

-- Returns:
    `SliderObject` with `:Set(newValue)` method.

-- Example:
    MainTab:CreateSlider({
        Name = "WalkSpeed",
        Range = {16, 200},
        Increment = 1,
        Suffix = "speed",
        CurrentValue = 16,
        Flag = "WalkSpeedValue",
        Callback = function(Value)
            game.Players.LocalPlayer.Character.Humanoid.WalkSpeed = Value
        end,
    })

---
-- 3.4: Text Input
---
`TabObject:CreateInput(SettingsTable)`

-- SettingsTable Fields:
    `Name` (string): The label for the input box.
    `PlaceholderText` (string, optional): The grayed-out text shown when the box is empty.
    `RemoveTextAfterFocusLost` (boolean, optional): If true, the text is cleared after the user presses Enter.
    `Callback` (function): Called when the user presses Enter. Receives the text content.

-- Returns:
    `InputObject` with `:Set(newText)` method.

-- Example:
    MainTab:CreateInput({
        Name = "Teleport to Player",
        PlaceholderText = "Enter username...",
        RemoveTextAfterFocusLost = true,
        Callback = function(Text)
            print("Teleporting to:", Text)
            -- Your teleport logic here
        end,
    })

---
-- 3.5: Keybind
---
`TabObject:CreateKeybind(SettingsTable)`

-- SettingsTable Fields:
    `Name` (string): The label for the keybind.
    `CurrentKeybind` (string, optional): The default keybind (e.g., "E"). Must be a valid `Enum.KeyCode` name.
    `Flag` (string, optional): A unique identifier for saving.
    `Callback` (function): Called when the key is pressed.

-- Returns:
    `KeybindObject` with `:Set(newKey)` method.

-- Example:
    MainTab:CreateKeybind({
        Name = "Toggle UI",
        CurrentKeybind = "K",
        Flag = "ToggleUIKeybind",
        Callback = function()
            -- Note: Rayfield has a built-in toggle on 'K'. This is for custom actions.
            print("UI toggle key pressed!")
        end,
    })

---
-- 3.6: Dropdown
---
`TabObject:CreateDropdown(SettingsTable)`

-- SettingsTable Fields:
    `Name` (string): The label for the dropdown.
    `Options` (table): A list of strings representing the choices in the dropdown.
    `CurrentOption` (string or table, optional): The default selected option(s).
    `MultipleOptions` (boolean, optional): If true, the user can select multiple options.
    `Flag` (string, optional): A unique identifier for saving.
    `Callback` (function): Called when the selection changes. Receives a table of the currently selected options.

-- Returns:
    `DropdownObject` with `:Set(newOptions)` method.

-- Example:
    VisualsTab:CreateDropdown({
        Name = "ESP Target",
        Options = {"Players", "Enemies", "Chests"},
        CurrentOption = {"Players"},
        MultipleOptions = true,
        Flag = "ESPTargets",
        Callback = function(Options)
            print("Selected ESP targets:", table.concat(Options, ", "))
        end,
    })

---
-- 3.7: Color Picker
---
`TabObject:CreateColorPicker(SettingsTable)`

-- SettingsTable Fields:
    `Name` (string): The label for the color picker.
    `Color` (Color3, optional): The default color. Defaults to white.
    `Flag` (string, optional): A unique identifier for saving.
    `Callback` (function): Called when the color is changed. Receives the new `Color3` value.

-- Returns:
    `ColorPickerObject` with `:Set(newColor)` method.

-- Example:
    VisualsTab:CreateColorPicker({
        Name = "ESP Box Color",
        Color = Color3.fromRGB(255, 0, 255),
        Flag = "ESPBoxColor",
        Callback = function(Color)
            print("New ESP color is:", Color)
            -- Update your ESP color here
        end,
    })

---
-- 3.8: Label & Paragraph
---
`TabObject:CreateLabel(Text)`
`TabObject:CreateParagraph(SettingsTable)`

-- `CreateLabel` Arguments:
    `Text` (string): The text to display.
-- `CreateParagraph` SettingsTable:
    `Title` (string): The title of the paragraph.
    `Content` (string): The main body text of the paragraph.

-- Returns:
    `LabelObject`/`ParagraphObject` with a `:Set()` method to update content.

-- Example:
    MainTab:CreateLabel("Welcome to my script!")
    MainTab:CreateParagraph({
        Title = "Instructions",
        Content = "This script has many features. Use the toggles and sliders below to control them. Have fun!"
    })

-------------------------------------------------------------------------------------------------------------------------
-- // Section 4: Other Library Functions
-------------------------------------------------------------------------------------------------------------------------

The library provides a few other useful top-level functions.

---
-- 4.1: Notification
---
`Library:Notify(SettingsTable)`

Shows a notification pop-up at the bottom of the screen.

-- SettingsTable Fields:
    `Title` (string): The title of the notification.
    `Content` (string): The body text of the notification.
    `Duration` (number, optional): How long the notification stays on screen. Defaults to 6.5 seconds.
    `Image` (number, optional): A Roblox asset ID for an icon.
    `Actions` (table, optional): A table of buttons to add to the notification.
        - Each action is a table: `{Name = "Button Text", Callback = function() ... end}`

-- Example:
    Library:Notify({
        Title = "Script Loaded",
        Content = "Welcome! Everything is ready.",
        Duration = 5,
        Image = 4483362458
    })

---
-- 4.2: Change Theme
---
`Library:ChangeTheme(ThemeName)`

Changes the UI's theme.

-- Arguments:
    `ThemeName` (string): The name of the theme to switch to. Currently "Default" or "Light".

-- Example:
    Library:ChangeTheme("Light")

---
-- 4.3: Destroy
---
`Library:Destroy()`

Completely removes the UI from the game.

-- Example:
    -- In an 'unload' button:
    MainTab:CreateButton({
        Name = "Unload Script",
        Callback = function()
            Library:Destroy()
        end
    })

=========================================================================================================================
End of Documentation
=========================================================================================================================
]]

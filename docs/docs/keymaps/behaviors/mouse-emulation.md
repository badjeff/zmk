---
title: Mouse Emulation Behaviors
sidebar_label: Mouse Emulation
---

## Summary

Mouse emulation behaviors send mouse events, including mouse button presses, cursor movement and scrolling.

:::warning[Refreshing the HID descriptor]

Enabling or disabling the mouse emulation feature modifies the HID report descriptor and requires it to be [refreshed](../../features/bluetooth.md#refreshing-the-hid-descriptor).
The mouse functionality will not work over BLE until that is done.

:::

## Configuration Option

To use any of the behaviors documented here, the ZMK mouse feature must be enabled explicitly via a config option:

```
CONFIG_ZMK_MOUSE=y
```

## Mouse Button Defines

To make it easier to encode the HID mouse button numeric values, include
the [`dt-bindings/zmk/mouse.h`](https://github.com/zmkfirmware/zmk/blob/main/app/include/dt-bindings/zmk/mouse.h) header
provided by ZMK near the top:

```
#include <dt-bindings/zmk/mouse.h>
```

## Mouse Button Press

This behavior can press/release up to 5 mouse buttons.

### Behavior Binding

- Reference: `&mkp`
- Parameter: A `uint8` with bits 0 through 4 each referring to a button.

The following defines can be passed for the parameter:

| Define        | Action         |
| :------------ | :------------- |
| `MB1`, `LCLK` | Left click     |
| `MB2`, `RCLK` | Right click    |
| `MB3`, `MCLK` | Middle click   |
| `MB4`         | Mouse button 4 |
| `MB5`         | Mouse button 5 |

Mouse buttons 4 and 5 typically map to "back" and "forward" actions in most applications.

### Examples

The following will send a left click press when the binding is triggered:

```
&mkp LCLK
```

This example will send press of the fourth mouse button when the binding is triggered:

```
&mkp MB4
```

### Input Processors

If you want to apply any [input processors](../input-processors/index.md#input-processors-overview) to `&mkp` you can do so by referencing `&mkp_input_listener`, e.g.:

```dts
&mkp_input_listener {
    input-processors = <&zip_temp_layer 2 2000>;
}
```

## Mouse Move

This behavior sends mouse X/Y movement events to the connected host.

### Behavior Binding

- Reference: `&mmv`
- Parameter: A `uint32` with 16-bits each used for vertical and horizontal velocity.

The following defines can be passed for the parameter:

| Define       | Action     |
| :----------- | :--------- |
| `MOVE_UP`    | Move up    |
| `MOVE_DOWN`  | Move down  |
| `MOVE_LEFT`  | Move left  |
| `MOVE_RIGHT` | Move right |

### Examples

The following will send a down mouse movement event to the host when pressed/held:

```
&mmv MOVE_DOWN
```

The following will send a left mouse movement event to the host when pressed/held:

```
&mmv MOVE_LEFT
```

### Input Processors

If you want to apply any [input processors](../input-processors/index.md#input-processors-overview) to `&mmv` you can do so by referencing `&mmv_input_listener`, e.g.:

```dts
&mmv_input_listener {
    input-processors = <&zip_temp_layer 2 2000>;
}
```

## Mouse Scroll

This behavior sends vertical and horizontal scroll events to the connected host.

### Behavior Binding

- Reference: `&msc`
- Parameter: A `uint32` with 16-bits each used for vertical and horizontal velocity.

The following defines can be passed for the parameter:

| Define       | Action       |
| :----------- | :----------- |
| `SCRL_UP`    | Scroll up    |
| `SCRL_DOWN`  | Scroll down  |
| `SCRL_LEFT`  | Scroll left  |
| `SCRL_RIGHT` | Scroll right |

### Examples

The following will send a scroll down event to the host when pressed/held:

```
&msc SCRL_DOWN
```

The following will send a scroll left event to the host when pressed/held:

```
&msc SCRL_LEFT
```

:::note

If you enabled [smooth scrolling](../../config/pointers.md#kconfig) then you will want to use the same `MOVE_UP`, `MOVE_DOWN`, etc values instead of the smaller `SCRL_*` parameters.

:::

### Input Processors

If you want to apply any [input processors](../input-processors/index.md#input-processors-overview) to `&msc` you can do so by referencing `&msc_input_listener`, e.g.:

```dts
&msc_input_listener {
    input-processors = <&zip_temp_layer 2 2000>;
}
```

### Advanced Configuration

Both `&mmv` and `&msc` are instances of the same `"zmk,behavior-input-two-axis"` behavior. As such, the following settings can be applied to either behavior, e.g.:

```dts
&mmv {
    trigger-period-ms = <12>
};
```

#### `trigger-period-ms`

How many milliseconds between generated input events based on the current speed/direction.

#### `delay-ms`

How many milliseconds to delay any processing or event generation when first pressed.

#### `time-to-max-speed-ms`

How many milliseconds it takes to accelerate to the curren max speed.

#### `acceleration-exponent`

The acceleration exponent to apply, with three possible values:

- `0` - uniform speed
- `1` - uniform acceleration
- `2` - exponential acceleration

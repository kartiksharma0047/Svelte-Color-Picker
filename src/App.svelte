<script>
  import Slider from "./Slider.svelte";
  import {
    rgbToHex,
    rgbToHsl,
    rgbToHsv,
    rgbToCmyk,
    hslToRgb,
    hsvToRgb,
    cmykToRgb,
  } from "./helpers/color-helpers.js";

  // Initial RGB values
  let red = 100;
  let green = 0;
  let blue = 100;

  // Selected color format
  let selectedFormat = "RGB";
  let colorInput = ""; // The input value for the selected color format

  // Derived color formats
  $: hexColor = rgbToHex(red, green, blue);
  $: hslColor = rgbToHsl(red, green, blue);
  $: hsvColor = rgbToHsv(red, green, blue);
  $: cmykColor = rgbToCmyk(red, green, blue);

  // Clamp values to ensure they are within valid ranges
  function clamp(value) {
    return Math.max(0, Math.min(255, value));
  }

  // Handle input value changes based on the selected format
  function updateColorFromInput() {
    try {
      let r, g, b;

      // Parsing the color input based on the selected format
      switch (selectedFormat) {
        case "RGB":
          [r, g, b] = colorInput.split(",").map((v) => clamp(parseInt(v.trim())));
          if (r !== undefined && g !== undefined && b !== undefined) {
            red = r;
            green = g;
            blue = b;
          }
          break;

        case "HEX":
          if (colorInput.startsWith("#")) {
            const bigint = parseInt(colorInput.slice(1), 16);
            red = clamp((bigint >> 16) & 255);
            green = clamp((bigint >> 8) & 255);
            blue = clamp(bigint & 255);
          }
          break;

        case "HSL":
          const [h, s, l] = colorInput.split(",").map((v) => parseInt(v.trim()));
          if (h !== undefined && s !== undefined && l !== undefined) {
            const rgb = hslToRgb(h, s, l);
            red = clamp(rgb.r);
            green = clamp(rgb.g);
            blue = clamp(rgb.b);
          }
          break;

        case "HSV":
          const [hsvH, hsvS, hsvV] = colorInput.split(",").map((v) => parseInt(v.trim()));
          if (hsvH !== undefined && hsvS !== undefined && hsvV !== undefined) {
            const rgb = hsvToRgb(hsvH, hsvS, hsvV);
            red = clamp(rgb.r);
            green = clamp(rgb.g);
            blue = clamp(rgb.b);
          }
          break;

        case "CMYK":
          const [c, m, y, k] = colorInput.split(",").map((v) => parseFloat(v.trim()));
          if (c !== undefined && m !== undefined && y !== undefined && k !== undefined) {
            const rgb = cmykToRgb(c, m, y, k);
            red = clamp(rgb.r);
            green = clamp(rgb.g);
            blue = clamp(rgb.b);
          }
          break;
      }
    } catch (error) {
      console.error("Invalid color input format");
    }
  }
</script>

<!-- Color Picker Controls -->
<div bp="grid">
  <div bp="10 offset-2">
    <h1>Color Picker</h1>
  </div>
</div>

<!-- Sliders for RGB values -->
<div class="color-controls">
  <Slider bind:color={red} bgColor="#AA0000" />
  <Slider bind:color={green} bgColor="#00AA00" />
  <Slider bind:color={blue} bgColor="#0000AA" />
</div>

<!-- Color Display -->
<div bp="grid">
  <div
    style="background-color: rgb({red}, {green}, {blue})"
    class="color-display"
    bp="10 offset-2"
  />
</div>

<!-- Color Format Selector and Input -->
<div bp="grid">
  <div bp="10 offset-2" class="Select-Color">
    <div>
      <label for="color-format">Select Color Format:</label>
      <select id="color-format" bind:value={selectedFormat}>
        <option value="RGB">RGB</option>
        <option value="HEX">HEX</option>
        <option value="HSL">HSL</option>
        <option value="HSV">HSV</option>
        <option value="CMYK">CMYK</option>
      </select>
    </div>
    <div>
      <label for="color-value">Enter {selectedFormat} Value:</label>
      <input
        id="color-value"
        bind:value={colorInput}
        placeholder={selectedFormat === "RGB" ? "r,g,b" : selectedFormat}
        on:change={updateColorFromInput}
      />
    </div>
  </div>
</div>

<!-- Display all Color Formats -->
<div bp="grid" class="color-numbers">
  <div bp="offset-2 5">
    RGB
    <br />
    r={red}, g={green}, b={blue}
  </div>
  <div bp="5">
    HEX
    <br />
    {hexColor}
  </div>
</div>

<div bp="grid" class="color-numbers">
  <div bp="offset-2 5">
    HSL
    <br />
    {hslColor}
  </div>
  <div bp="5">
    HSV
    <br />
    {hsvColor}
  </div>
</div>

<div bp="grid" class="color-numbers">
  <div bp="offset-2 5">
    CMYK
    <br />
    {cmykColor}
  </div>
</div>

<style>
  .color-display {
    height: 200px;
  }
  .color-controls {
    margin-top: 20px;
  }
  .color-numbers {
    margin-top: 20px;
  }
  .color-numbers > div {
    height: 50px;
    border: solid 1px gray;
    border-radius: 2px;
    text-align: center;
    justify-content: space-around;
    padding: 5px;
  }
  .Select-Color {
    display: flex;
    align-items: center;
    justify-content: center;
  }
  .Select-Color div {
    margin: 0 2rem;
  }
</style>

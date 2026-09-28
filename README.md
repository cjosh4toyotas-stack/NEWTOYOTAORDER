# New Vehicle Order Confirmation

Client-facing order confirmation sheet for McGrath Toyota of Iowa City. It replaces the paper order acknowledgement with a single HTML page that knows Toyota's factory options, packages, colors, and model codes for each trim, so nothing gets missed between the client conversation and the order entry.

**Live:** GitHub Pages serves `index.html` at the repo's Pages URL.

## What it does

- **Model → Trim → Model code** dropdowns at the top fill the vehicle line. Model codes are trim-specific (FWD / AWD / 7- or 8-passenger).
- **Exterior and interior color** slots are dropdowns on their own lines, listing only the colors Toyota offers on the selected trim. Special colors are starred and footnoted at $475.
- **Factory Installed Options & Packages (FIO)** fills with every option and package for the trim — code, description, MSRP — each with **Must have / Flexible / No** boxes. Lines with available options must be answered to proceed.
- **PC (special color) logic:** if every exterior color chosen is a special color, Must have checks itself. If the client mixes special and standard colors, Flexible checks itself. The other boxes gray out either way.
- **Post Production Options (PPO) & Dealer Installed Options (DIO)** — six write-in lines.
- **Timing and price** — estimated delivery, estimated MSRP, and the client's choice between accepting additional options to shorten the wait or waiting for exactly what they requested.
- **Page 2** — terms (allocation, price, delivery, partial payment, order accuracy), an acknowledgement note, client and consultant signatures, and a dealership-use box for order entry and verification.

Every write-in field is click-to-type on screen. Checkboxes toggle on click. The date fills automatically. Client signature, initial, and date lines print in blue; everything else in black and Toyota red.

## Printing

Pick the model, trim, and code, fill what you need, then use the **Print** button in the toolbar (or Cmd/Ctrl-P). The toolbar does not print. Output is two Letter pages.

## Adding a model

All product data lives in the `DATA` object near the bottom of `index.html`. Each trim is one block:

```js
"XLE": {
  ext: ["01L5","03T3","0218","06X5","0089","08X8"],   // exterior color codes offered on this trim
  int: ["EA15"],                                      // interior color codes offered on this trim
  codes: ["5408 FWD 7-pass","5406 FWD 8-pass","5407 AWD"],
  fio: [
    ["AA","18-in. alloy wheels (5408, 5407 only)","$540"],
    ["AC","1500W inverter — requires XLE Plus pkg","$300"],
    ["ST","Spare tire","$75"],
    ["EY","Entertainment Package — 11.6-in. rear display, 2 wireless headphones","$1,415"],
    ["PC","Special color — Heavy Metal, Ruby Flare, Wind Chill","$475"]
  ]
}
```

Color names come from the `EXT` and `INT` maps above `DATA`; add any new code there once and reference it from every trim that offers it. Put an asterisk after the name of any special color and add its code to `PCCOLORS` so the PC logic recognizes it.

Source for every value is the Options & Packages and Colors tabs of Toyota's dealer configurator for that model year.

## Models loaded

- 2026 Sienna — LE, XLE, XSE, Limited, Woodland Edition, Platinum
- 2026 Tacoma — SR, SR5, TRD PreRunner, TRD Sport, TRD Off-Road, Limited, Trailhunter, TRD Pro (27 model codes; i-FORCE MAX variants are listed under their trim as model codes)

### Model-code-driven data (Tacoma)

Tacoma options, packages, and colors vary by model code, not just trim, so the Tacoma block uses `byCode: true`. Each FIO line carries a fourth element — the model codes it applies to — and colors are keyed by model code in `extBy` / `intBy`. The sheet stays blank until a model code is chosen, then loads only what Toyota lists for that code. Packages that bundle stand-alone options are mapped in `INCLUDES` (e.g. `OF` → CY, MR, EE, EF); when a package is marked Must have or Flexible, those option lines gray out and read "Included in OF." The PC (special color) line is generated automatically from the extra-cost colors available on that code, with price from the `PC` map.

Source: `engage.toyota.com` — `/api/vehicle/optionsPackages/tacoma/2026`, `/api/vapi/getVehicleColors`, and `/api/vapi/getVehicleData` (model code list). Pulled Sep 27, 2026.

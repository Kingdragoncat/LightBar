# LightBar

simple extendable light bar meant to be mounted to whatever project you want. just chain em together and go

## what is this thing

its a PCB with 21 WS2812B addressable LEDs on it. theres connectors on both ends so you can daisy chain multiple bars together. each LED has its own 100nF bypass cap. theres also mounting holes on each end so you can screw it down to stuff

![front render](images/render_front.png)

![back render](images/render_back.png)

## pcb layout

![pcb front](images/pcb_front.png)

![pcb back](images/pcb_back.png)

## schematic

![schematic](images/schematic.png)

## specs

- 21x WS2812B addressable RGB LEDs
- 5V power
- data in/out connectors on both ends for daisy chaining
- 100nF decoupling cap per LED
- 47uF bulk cap on the power rail
- 330 ohm resistors on data lines
- mounting holes on both ends
- designed in KiCad

## wiring

hook up 5V, GND, and data to the input connector and youre good. if you want more bars just connect the output of one to the input of the next

## BOM

| Ref | Qty | Value | Package | Description | Link |
|-----|-----|-------|---------|-------------|------|
| D1-D21 | 21 | WS2812B | 5050 PLCC4 | Addressable RGB LED | [LCSC](https://www.lcsc.com/product-detail/Light-Emitting-Diodes-LED_Worldsemi-WS2812B-B-W_C2761795.html) |
| C1-C21 | 21 | 100nF | 1206 | Ceramic decoupling cap | [LCSC](https://www.lcsc.com/product-detail/Walsin-1206B104K500CT_C93194.html) |
| C22 | 1 | 47uF | 1210 | Ceramic bulk cap | [LCSC](https://www.lcsc.com/product-detail/TORCH-CT4G-1210-X5R-16V-47uF-K_C2960960.html) |
| R1, R2 | 2 | 330Ω | 1206 | SMD resistor | [LCSC](https://www.lcsc.com/search?q=1206%20330%20ohm) |
| J1, J2 | 2 | Conn_01x03 | JST XH 3-pin | Through-hole connector (S3B-XH-A) | [LCSC](https://www.lcsc.com/product-detail/JST-S3B-XH-A-LF-SN_C157928.html) |

**total: 47 parts**

## license

do whatever you want with it honestly

---

made by Jonah Novoseller (Kingdragoncat)

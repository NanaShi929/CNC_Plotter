# DIY CNC Plotter

<p align="center">
<img width="32%" height="225" alt="IMG_20260805_120614 jpg (Small)" src="https://github.com/user-attachments/assets/f9eddb5c-50c6-4cc2-96ca-5a4e7bd38175" />
<img width="32%" height="225" alt="IMG_20260805_120633 jpg (Small)" src="https://github.com/user-attachments/assets/27431599-7ce0-4a9e-ad43-38142b0b89b7" />
<img width="32%" height="225" alt="IMG_20260805_121102 jpg (Small)" src="https://github.com/user-attachments/assets/5c33e2cc-5dd9-42c3-998d-b49e2ed5b97e" />

</p>

*A customizable Cartesian CNC plotter built with an Arduino, a CNC Shield, and 3D printed parts. Features hardware limit switches for automatic homing and a cloned A-axis driver to power a dual-motor X-axis for extra torque.*

## 🛠️ Bill of Materials (BOM)

### Electronics
* 1x Arduino Uno
* 1x CNC Shield V3
* 3x DRV8825 Stepper Motor Drivers
* 3x NEMA 17 Stepper Motors (X-axis, Y-axis, and X-axis Duplicate)
* 1x Servo Motor (Pen lift mechanism)
* 4x Limit Switches (for -X, +X, -Y, and +Y homing/limits)
* 1x 12V 5Amp Power Supply
* Wire and Female DuPont Connectors
* 2x Jumper Caps (for cloning the axis)

### Mechanical & Hardware
* 3D Printed Parts 
  * *Note: The reference designs for these plotters often use 10mm rods. Because this build uses 8mm rods, you must use modified STL files or print 10mm-to-8mm adapter sleeves for the rods and bearings to fit securely.*
* 8mm and 5mm Smooth Rods
* Linear Motion Bearings (sized for 8mm and 5mm rods)
* GT2 Timing Belt 
* Bore Timing Pulleys
* Idler Pulleys
* Fasteners (M3/M5 screws and nuts)

---

## ⚡ Electronics & Wiring

![Circuit Diagram](https://github.com/user-attachments/assets/4e67b3c2-b368-427c-bc07-2b476cf450c5)

1. **Mount the Shield:** Plug the CNC Shield V3 directly into the top of the Arduino Uno.
2. **Install Drivers (CRITICAL STEP):** Insert the three DRV8825 drivers into the **X**, **Y**, and **A** slots. Leave the Z slot empty.
   * ⚠️ **WARNING:** DRV8825 drivers are inserted in the *opposite* orientation of A4988 drivers! Make sure the small potentiometer (tuning screw) is facing the **USB port/power jack side** of the Arduino. Plugging them in backwards will destroy the drivers and the shield.
3. **Clone the X-Axis:** Place two jumper caps on the designated **Jumper For cloning axis** block. Place them across the pins that map the A-axis step/dir signals to the X-axis so both X motors move in sync.
4. **Connect Steppers:** 
   * Connect the primary X-axis NEMA 17 to the X driver pins.
   * Connect the Y-axis NEMA 17 to the Y driver pins.
   * Connect the Duplicate X-axis NEMA 17 to the A driver pins.
5. **Connect the Servo (Pen Lift):** Connect the Servo to the **Z+** pins (Power, GND, and Signal). 
6. **Wire the Limit Switches:** 
   * Connect wires to the **Normally Open (NO)** and **Common (C)** tabs on your limit switches.
   * **-X Switch:** Plug into the pins labeled **X-** on the CNC Shield.
   * **+X Switch:** Plug into the pins labeled **X+** on the CNC Shield.
   * **-Y Switch:** Plug into the pins labeled **Y-** on the CNC Shield.
   * **+Y Switch:** Plug into the pins labeled **Y+** on the CNC Shield.
7. **Power:** Connect the 12V-36V adapter strictly to the blue terminal block on the CNC Shield. 

---

## 🔧 Mechanical Assembly

> **Adapting for 8mm Rods:** Since the original 3D printed parts for this type of gantry are typically modeled for 10mm rods, ensure you have properly adapted the mounting holes via modified STL files or 10mm-to-8mm shims.

### 1. The Axes Setup
* Assemble the 8mm and 5mm smooth rods, linear motion bearings, and adapted 3D printed parts for the axes. 
* Mount the three NEMA 17 motors, attach the bore timing pulleys to their shafts, and route the GT2 timing belts around the idler pulleys. Ensure all belts are properly tensioned.

### 2. Pen Lift Mechanism
* Attach the pen-lifting 3D parts to the carriage and mount the servo so it pushes the pen down to draw and releases to lift.

### 3. Mounting the Limit Switches
* Mount your four contact switches at the physical extremes of your axes. Ensure the carriage clicks them just before hitting the physical boundaries of the rails on both ends of the X and Y axes.

---

## 💻 Software Setup

> **📁 Repository Files Note:** 
> * **`grbl-mi`** is the required Arduino library for this project.
> * **`Inkscape.zip`** contains the correct Inkscape software along with its necessary G-Code generation extension.

### 1. Install Arduino IDE & Flash GRBL
1. Download and install the latest [Arduino IDE](https://www.arduino.cc/en/software).
2. Download the **`grbl-mi`** folder from this repository (this is your Arduino library).
3. Open the Arduino IDE, go to `Sketch` -> `Include Library` -> `Add .ZIP Library` and select your downloaded GRBL file (you may need to zip the `grbl-mi` folder if you downloaded it directly).
4. Go to `File` -> `Examples` -> `grbl-mi` -> `grblUpload`.
5. Connect your Arduino via USB. Select **Arduino Uno** and the correct **COM Port**, then click **Upload**.

### 2. Configure Universal G-Code Sender (UGS)
1. Download [Universal G-Code Sender](https://winder.github.io/ugs_website/download/).
2. Run `ugsplatform.exe`.
3. Set the **Firmware** to `GRBL`, **Baud Rate** to `115200`, select your COM port, and click **Connect**.
4. **Enable Homing:** Open the console in UGS, type `$22=1`, and press Enter. 
5. Use the **UGS Setup Wizard** to:
   * Check motor directions (ensure both X motors spin the same way so they don't fight each other).
   * Calibrate steps/mm using a ruler.

### 3. Generate G-Code with Inkscape
1. Extract the **`Inkscape.zip`** file provided in this repository. Install the Inkscape software inside, which already includes the necessary extension for plotting.
2. Set your canvas size to match your plotter's physical limits.
3. Import an image, go to `Path` -> `Trace Bitmap`, then `Path` -> `Object to Path`.
4. Use the extension menu to generate the `.gcode` file, setting your servo angles (e.g., Pen Up: 120, Pen Down: 90).

## 🚀 Running Your First Plot
1. Clamp down your paper securely.
2. **Click the "$H" (Home) button in UGS.** The machine will automatically move until it clicks the limit switches, establishing a perfect `0,0` origin point.
3. Load your generated `.gcode` file into UGS.
4. Click **Play** and watch it draw!

---

## 🖼️ Test Plots & Results

Here are a few examples of the plotter in action:

<p align="center">
  <img src="https://github.com/user-attachments/assets/ded52a3e-95f8-4d31-b341-c564d1753413" width="45%" />
</p>

---

## 📚 References & Credits

This project was built by referencing several excellent tutorials from the maker community. I highly recommend checking out these videos for visual guides on the build process:

* **Base Mechanical Build:** [DIY Large CNC Plotter | Writing & Drawing Machine by Coders Cafe](https://youtu.be/5qAwCg7XPZw?si=-M1tp8hS7q4-0wvH)
* **Limit Switch & Homing Integration:** [Super Easy 3D Printed Arduino CNC Drawing Machine by DIY Machines](https://youtu.be/XYqx5wg4oLU?si=ua8uZhmxj5AIle0q)
* **Software Configuration Guide:** [How To Setup and Use CNC Pen Plotter Machine - Software Guide by DIY Active](https://youtu.be/IGkHnAI-8M4?si=8v4sWS1JTCHgB7Xx)

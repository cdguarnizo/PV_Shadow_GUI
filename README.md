# Partial Shading Simulator for Photovoltaic Modules 

Graphical User Interface (GUI) developed in Python that models the electrical behavior of a photovoltaic module under **partial shading** conditions, using the **two-diode reverse-breakdown model (Bishop model)**.

It allows you to estimate the circuit parameters of a panel based on its data sheet, configure an irradiance pattern cell by cell, and view the resulting **I–V** and **P–V** curves in real time.

## Features

- Manual entry of panel parameters or selection from a predefined catalog (MSX60, SM55, ST40, KGC200GT, STP280, SPR-315, I-110/24).
- Automatic estimation of the parameters of the two-diode equivalent circuit (Iph, Io1, Io2, Rs, Rp) using the Newton-Raphson method.
- Manual editing of the estimated parameters and reverse breakdown parameters (aRBD, VRBD, nRBD).
- Interactive cell grid: the irradiance of each cell is adjusted using the mouse wheel.
- Panel temperature control via a slider.
- Electrical simulation of the module using [`pvmismatch`](https://github.com/SunPower/PVMismatch), including the effect of bypass diodes.
- Live display of the I–V and P–V curves, with the maximum power point (MPP) marked.
- Export results to Excel (`.xlsx`) and graphs to PNG.

## Project Structure

```
gui_sompreado_pv/
├── main.py             # Main Window and Interface Logic (PySide6)
├── menu_actions.py      # Menu options: Export to Excel/PNG, “About” window
├── estimacion_pv.py     # Estimation of Parameters for the Two-Diode Model
├── assets/               # Images used in the interface (circuit model, Bishop model)
├── SimuladorPV.spec     # Configuring PyInstaller to Generate the Executable
└── requirements.txt      # Python dependencies with fixed versions
```

## Requirements

- **Python 3.11** (tested with version 3.11.9). Backward compatibility with versions prior to 3.10 is not guaranteed, since `numpy` and `scipy` in the versions used here require it.
- **Operating Systems:** Developed and tested on Windows 11. It should work on Linux and macOS since it is written entirely in Python and Qt, but it has not been verified in those environments.
- Graphical connection (desktop environment) to display the Qt interface.

### Libraries

| Library | Versión | Description |
|---|---|---|
| [PySide6](https://pypi.org/project/PySide6/) | 6.10.1 | Graphical interface (windows, tabs, widgets, menus) |
| [numpy](https://pypi.org/project/numpy/) | 2.4.2 | Numerical Calculation and Handling of the Irradiance Matrix |
| [scipy](https://pypi.org/project/scipy/) | 1.17.0 | Numerical dependency required by `pvmismatch` |
| [matplotlib](https://pypi.org/project/matplotlib/) | 3.10.8 | I–V and P–V curve graphs embedded in the interface |
| [openpyxl](https://pypi.org/project/openpyxl/) | 3.1.5 | Generating Excel files (`.xlsx`) with the results |
| [pvmismatch](https://pypi.org/project/pvmismatch/) | 4.1 | Electrical Simulation Engine for Photovoltaic Modules (Shading Mismatch) |

All exact versions are listed at [`requirements.txt`](requirements.txt).

## Installation

1. **Clone the repository**

   ```bash
   git clone https://github.com/Maritru732/gui_sombreado_pv.git
   cd gui_sombreado_pv
   ```

2. **Build and activate the virtual environment** (recommended)

   In Windows (PowerShell):

   ```powershell
   python -m venv venv
   .\venv\Scripts\Activate.ps1
   ```

   In Linux/macOS:

   ```bash
   python3 -m venv venv
   source venv/bin/activate
   ```

3. **Install dependencies**

   ```bash
   pip install -r requirements.txt
   ```

## Excecute

With the Virtual environment activated:

```bash
python main.py
```

Se abrirá la ventana principal con dos pestañas:

1. **Configuración:** seleccione un panel del catálogo o ingrese sus parámetros manualmente, presione **"Estimar parámetros"**, revise/ajuste los resultados y presione **"Guardar cambios"** para construir el modelo eléctrico.
2. **Sombreado:** use la rueda del mouse sobre cada celda de la cuadrícula para modificar su irradiancia (pasos de 50 W/m²) y observe cómo cambian las curvas I–V y P–V en tiempo real. También puede ajustar la temperatura del panel con el deslizador.

Desde el menú **"Menú"** puede exportar los parámetros del panel, los parámetros estimados, la matriz de sombreado y las curvas, en Excel o PNG.

## Generar el ejecutable (opcional)

El proyecto incluye [`SimuladorPV.spec`](SimuladorPV.spec) para empaquetar la aplicación con [PyInstaller](https://pyinstaller.org/):

```bash
pip install pyinstaller==6.21.0
pyinstaller SimuladorPV.spec
```

El ejecutable se genera en `dist/SimuladorPV/`. Esta carpeta y `build/` no se versionan en el repositorio (ver [`.gitignore`](.gitignore)).

## Fundamento teórico

El simulador se basa en el modelo de dos diodos:

```
I = Iph − Io1·[exp((V+I·Rs)/(a1·Vt)) − 1] − Io2·[exp((V+I·Rs)/(a2·Vt)) − 1] − (V+I·Rs)/Rp
```

extendido con el modelo de **Bishop** para representar el efecto de avalancha en polarización inversa (crítico para celdas sombreadas):

```
I = Iph − Io1·[exp((V+I·Rs)/(a1·Vt))−1] − Io2·[exp((V+I·Rs)/(a2·Vt))−1]
    − (V+I·Rs)/Rp · { 1 + aRBD · [1 − (V+I·Rs)/VRBD]^(−nRBD) }
```

**Referencia:** Bishop, J. W. (1988). *Computer simulation of the effects of electrical mismatches in photovoltaic cell interconnection circuits.* Solar Cells, 25(1), 73–89.

Más detalle sobre el flujo de uso y las variables del modelo está disponible dentro de la propia aplicación, en **Menú → Acerca de**.

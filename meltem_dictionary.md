# Meltem Wind Farm: Data Dictionary

**Story.** The Meltem wind farm on the Aegean coast has 12 turbines (`T01` to `T12`). The *meltem* is the strong summer wind of the Aegean. The control system writes one record per turbine per day. The file `meltem_daily.csv` holds 1,827 days (2020-01-01 to 2024-12-31) for each of the 12 turbines: 21,924 rows. Every column is a daily value, either a daily mean or the daily total, as given below.

| Column | Unit | Meaning |
|---|---|---|
| `date` | | day of the record |
| `turbine` | | turbine id, `T01` ... `T12` |
| `ambient_c` | °C | outside air temperature at the turbine |
| `energy_mwh` | MWh | energy delivered to the grid that day |
| `rotor_rpm` | rpm | rotor speed |
| `gen_rpm` | rpm | generator speed |
| `gbx_oil_c` | °C | gearbox oil temperature |
| `gbx_brg_c` | °C | gearbox main bearing temperature (drive end) |
| `gbx_brg_nde_c` | °C | gearbox bearing temperature (non-drive end) |
| `gen_wind_u_c`, `gen_wind_v_c`, `gen_wind_w_c` | °C | generator winding temperatures, phases U, V, W |
| `gen_bearing_c` | °C | generator bearing temperature |
| `nacelle_c` | °C | nacelle (housing) air temperature |
| `hub_c` | °C | rotor hub temperature |
| `converter_c` | °C | power converter temperature |
| `transformer_c` | °C | transformer temperature |
| `pitch_1_deg`, `pitch_2_deg`, `pitch_3_deg` | degrees | blade pitch angles of the three blades |
| `yaw_err_deg` | degrees | yaw misalignment (angle between the nacelle and the wind) |
| `vib_nac_x_mm_s`, `vib_nac_y_mm_s` | mm/s | nacelle vibration, x and y direction |
| `vib_tower_mm_s` | mm/s | tower vibration |
| `grid_voltage_v` | V | grid voltage |
| `grid_freq_hz` | Hz | grid frequency |
| `reactive_kvar` | kvar | reactive power |
| `wind_dir_deg` | degrees | wind direction |
| `humidity_pct` | % | relative humidity |
| `pressure_hpa` | hPa | air pressure |
| `wind_ms` | m/s | wind speed measured by the nacelle anemometer |
| `gen_temp_c` | °C | generator housing temperature |
| `vib_gbx_mm_s` | mm/s | gearbox vibration (RMS velocity). Measuring range of the sensor: 0 to 9 mm/s |
| `fault_next7` | 0/1 | 1 if the turbine's gearbox is taken out of service for repair within the next 7 days (not counting the day itself), 0 otherwise |

Some columns contain gaps. Finding them is part of the work.

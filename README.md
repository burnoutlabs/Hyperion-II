# Hyperion-II

> **Provenance.** This repository is a standalone copy of
> [werocketry/Hyperion-II](https://github.com/werocketry/Hyperion-II), carried
> over with its full history. It is not kept in sync with the original, which
> is left as it was. The commit author email was rewritten to a GitHub noreply
> address during the copy, so commit hashes here differ from the original's
> even though the history is the same. This copy is the one that is maintained.

This repository contains the resources, designs, and analyses for the Hyperion-II rocket project (Western Engineering Rocketry, 2024-25 season), which flew on 2025-08-21. It is organized into several folders, each dedicated to a specific aspect of the project. Below is a breakdown of the repository structure and its contents.

## Repository Details

Click [here](DSS.md) to see the design summary sheet!

### Structure

```
Hyperion-II
├── DSS.md                  design summary sheet
├── OpenRocket-23.09.jar    OpenRocket release used by the scripts (Git LFS)
├── flight-data
│   ├── fw_br_lr_analysis
│   └── fw_tm_analysis
├── ork
│   ├── outputs-v2
│   └── outputs-v3
├── schematics
│   └── avionics
└── testing
    └── 2024-11-24_compression_al_couplers
```

### `DSS.md`

The design summary sheet: airframe, recovery, avionics, airbrakes and motor choice.

### `OpenRocket-23.09.jar`

The OpenRocket 23.09 release, committed together with the `ork/` scripts. It is stored with Git LFS, so run `git lfs pull` after cloning.

### `flight-data/`

Flight logs from the 2025-08-21 flight (Featherweight Blue Raven and Altus Metrum TeleMega), as submitted to Launch Canada, plus event tables and plots derived from them. The complete dataset and the analysis pipeline live in [burnoutlabs/post-flight-analysis](https://github.com/burnoutlabs/post-flight-analysis). See [flight-data/README.md](flight-data/README.md) for what each file is and how the flight compared with the simulations.

### `ork/`

Holds the OpenRocket simulation files (`hyperion_II_v2.ork`, `hyperion_II_v3.ork`) and the Python scripts that use [orlab](https://github.com/heibench/orlab) for simulation analysis. `outputs-v2/` and `outputs-v3/` contain the generated plots and text summaries for the v2 and v3 models respectively. Once you have Python and a valid Java release installed, install `orlab` using:

```
pip install orlab
```

For detailed installation instructions see [heibench/orlab](https://github.com/heibench/orlab).

### `schematics/`

Avionics and recovery wiring: the KiCad project in `avionics/` (with its custom symbol libraries and backups), PDF exports dated 2024-12-31 and 2025-04-23, and the concept block diagram and draw.io source for an SRAD flight computer that did not progress past the concept stage.

### `testing/`

Physical tests. Each test has its own dated subfolder with its data, scripts and results.

- `2024-11-24_compression_al_couplers/`: aluminum coupler compression testing.

`testing/README.md` also lists tests that were planned for the season; only the coupler compression test has a folder here.

## Contributing

Contributions are welcome! Please fork the repository, make your changes, and submit a pull request. Ensure you adhere to the coding standards and provide detailed documentation for any new scripts or analyses.

## License

This project is licensed under the terms of the [MIT License](LICENSE).

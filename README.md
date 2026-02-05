# pi-jukebox-os
Professional RFID Jukebox: A Yocto Scarthgap project for Raspberry Pi 3B featuring hardware-triggered audio and industrial-grade OS orchestration.


## Project structure



jukebox-os-manifest/
├── kas-project.yml              # Build orchestration config
├── README.md                    # Build & Flash instructions
├── ARCHITECTURE.md              # Hardware/Software stack detail
├── layers/
│   └── meta-jukebox/            # Your custom industrial layer
│       ├── conf/layer.conf
│       ├── recipes-apps/
│       │   └── jukebox/
│       │       ├── jukebox_1.0.bb
│       │       └── files/
│       │           ├── jukebox.service
│       │           └── shutdown-button.service
│       ├── recipes-core/
│       │   ├── images/
│       │   │   └── jukebox-image.bb
│       │   └── base-files/
│       │       └── base-files_%.bbappend
│       └── recipes-bsp/
│           └── bootfiles/
│               └── rpi-config_%.bbappend
└── src/                         # Application Source (Git Submodule or local)
    ├── jukebox.py
    ├── shutdown_button.py
    ├── jukebox_start.sh
    └── mappings.cfg
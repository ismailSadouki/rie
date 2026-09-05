Sionna RT gives us the **physics engine**:
```txt
                 Sionna RT
                     │
        ┌────────────┼────────────┐
        ↓            ↓            ↓
     rays       reflections    diffraction
        │            │            │
        └────────────┼────────────┘
                     ↓
              multipath channel
```

We provide the **world**:

```text
Our code
   │
   ├── ground
   ├── buildings
   └── receiver
          │
          ▼
      Sionna RT
```
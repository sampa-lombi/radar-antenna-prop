# Esquema de Conexiones - Antena Radar Prop

## Componentes principales
- **Batería**: 12V AGM 7Ah
- **Convertidor DC/DC**: LM2596 (12V → 5V)
- **Arduino**: Uno/Nano
- **Servo**: MG995
- **Módulos LED**: 2x módulos de 12V
- **MOSFET**: IRF520N
- **Resistencias**: 10kΩ (pull-down), 220Ω (gate)
- **Capacitores**: 100µF (entrada/salida LM2596), 100nF (bypass)
- **Fusibles**: 5A (entrada 12V), 2A (salida 5V)

---

## Diagrama General con LM2596

```
                    BATERÍA 12V AGM 7Ah
                           |
                           +========= FUSIBLE 5A
                           |
                 +-----+----+----+-----+
                 |     |        |     |
                 |     |        |     |
            [LM2596]   |        |     |
           12V→5V      |        |     |
             |         |        |     |
         +---+---+     |        |     |
         |       |     |        |     |
        GND     5V    GND      GND   +12V
         |       |     |        |     |
         |   +---+-----+---+    |     |
         |   |   Arduino   |    |     |
         |   |  D7  D9 5V  |    |     |
         |   |   GND       |    |     |
         |   +---+-----+---+    |     |
         |       |     |        |     |
         |     Servo  Servo    |     |
         |      D9   Signal    |     |
         |       |     |       |     |
        GND     5V   Signal   GND   +12V
         |       |     |        |     |
         |     +-------+        |     |
         |                      |     |
         |   [MOSFET IRF520N]   |     |
         |    Gate----D7        |     |
         |    Source---+--------+-----+
         |    Drain----+
         |             |
         +-----+-------+----------+
               |                  |
            (módulos LED 12V)     |
             +---- |----+         |
             |     |    |         |
            +12V  -V   +12V------+
```

---

## Esquema Detallado por Componentes

### 1. CONVERTIDOR LM2596 (12V → 5V)

```
       BATERÍA 12V
           |
         +12V
           |
    ┌------+-------┐
    |   LM2596     |
    |   IN    GND  |
    |    |     |   |
    |    +-----+   |
    |   VIN COM    |
    └------+-------┘
           |
        +--+--+
        |     |
      100µF  100nF  ← Capacitores entrada
        |     |
        |    GND
        |
        +-------- ADJ (ajuste de voltaje)
        |
       10kΩ ← Resistencia de ajuste
        |
       2.2kΩ
        |
       GND


    Salida:
    ┌------+-------┐
    |   LM2596     |
    |   OUT  GND   |
    |    |    |    |
    |    +----+    |
    |   VOUT COM   |
    └------+-------┘
           |
        +--+--+
        |     |
      100µF  100nF  ← Capacitores salida
        |     |
        |    GND
        |
       +5V (hacia Arduino y Servo)
```

### 2. Conexión Arduino - Servo - LEDs

```
┌─────────────────────────────────────────────────────────────┐
│                        ARDUINO UNO/NANO                      │
│                                                              │
│  D7  ──────────────────┐                                    │
│                        │                                    │
│  D9  ──────────────────┼──────► Servo Signal                │
│                        │                                    │
│  5V  ──────────────────┼──────► Servo VCC + LM2596 Out     │
│                        │                                    │
│  GND ──────────────────┼──────► Servo GND + LM2596 Com     │
│                        │                                    │
└────────────────────────┼────────────────────────────────────┘
                         │
                    220Ω Resistencia
                         │
                    Gate IRF520N
                         │
                     10kΩ │ (pull-down)
                         │
                        GND
```

### 3. Circuito del MOSFET + LEDs

```
              +12V (desde batería)
               │
               ├──────────────────────────┐
               │                          │
             ╔═╧═╕    LED Módulo 1      ╔═╧═╕    LED Módulo 2
             ║   │    (+12V → -V)       ║   │    (+12V → -V)
             ║   │    ┌─────┐           ║   │    ┌─────┐
             ╚═╤═╛    │ LED │           ╚═╤═╛    │ LED │
               │      └─────┘             │      └─────┘
               │         │                │         │
               └────┬────┴────┬───────────┴────┬────┘
                    │                         │
                  (-)                        (-)
                    │                         │
                    └────────┬────────────────┘
                             │
                          Drain
                        IRF520N
                             │
                    ┌─────────┴──────────┐
                    │                    │
                   Gate              Source
                IRF520N             IRF520N
                    │                    │
                 220Ω                   GND
                    │          (Common con batería)
                    │
                Arduino D7
```

---

## Tabla de Conexiones Completa

| Componente | Pin/Patilla | Conexión | Notas |
|-----------|-----------|----------|-------|
| **Batería 12V** | (+) | → Fusible 5A → LM2596 VIN | Principal |
| **Batería 12V** | (-) | → GND común | Retorno |
| **LM2596** | VIN | ← Batería 12V (+) | Entrada |
| **LM2596** | COM | ← Batería GND | Retorno entrada |
| **LM2596** | VOUT | → Arduino 5V + Servo VCC | Salida 5V |
| **LM2596** | COM (salida) | → Arduino GND + Servo GND | Retorno salida |
| **LM2596** | ADJ | ← Divisor resistivo 10k/2.2k | Ajuste voltaje |
| **Arduino** | D7 | → 220Ω → Gate IRF520N | Control MOSFET |
| **Arduino** | D9 | → Servo Signal | Control servo |
| **Arduino** | 5V | ← LM2596 VOUT | Alimentación |
| **Arduino** | GND | ← LM2596 COM (salida) | Retorno |
| **Servo MG995** | Signal | ← Arduino D9 | Señal PWM |
| **Servo MG995** | VCC | ← LM2596 VOUT (5V) | Alimentación |
| **Servo MG995** | GND | ← GND común | Retorno |
| **IRF520N** | Gate | ← Arduino D7 (vía 220Ω) | Control |
| **IRF520N** | Source | → GND común | Retorno |
| **IRF520N** | Drain | → LED(-) | Salida |
| **LED Módulos** | (+) | ← Batería 12V (+) | Alimentación |
| **LED Módulos** | (-) | ← IRF520N Drain | Control vía MOSFET |
| **Capacitores** | 100µF entrada | VIN a COM | Filtrado entrada |
| **Capacitores** | 100nF entrada | VIN a COM | Bypass entrada |
| **Capacitores** | 100µF salida | VOUT a COM | Filtrado salida |
| **Capacitores** | 100nF salida | VOUT a COM | Bypass salida |

---

## Configuración del LM2596 para 5V

El LM2596 requiere un divisor resistivo en el pin ADJ para establecer el voltaje de salida:

```
Fórmula:
VOUT = 1.23V × (1 + R1/R2)

Para 5V:
5 = 1.23 × (1 + R1/R2)
R1/R2 = (5/1.23) - 1 = 3.065

Valores comerciales recomendados:
- R1 = 10kΩ (entre VOUT y ADJ)
- R2 = 2.2kΩ (entre ADJ y GND)

Verificación: 5V = 1.23 × (1 + 10/2.2) = 1.23 × 5.545 = 6.82V ← Muy alto

Mejor opción:
- R1 = 3.9kΩ
- R2 = 2.2kΩ
Resultado: 5V = 1.23 × (1 + 3.9/2.2) = 1.23 × 2.773 = 3.41V ← Bajo

**Ideal:**
- R1 = 10kΩ
- R2 = 3.3kΩ
Resultado: 5V = 1.23 × (1 + 10/3.3) = 1.23 × 4.03 = 4.96V ✓

O usar potenciómetro de 10kΩ para ajuste fino.
```

---

## Diagrama ASCII Completo

```
┌─────────────────────────────────────────────────────────────────────────┐
│                         SISTEMA COMPLETO RADAR PROP                    │
└─────────────────────────────────────────────────────────────────────────┘

                          ┌─── BATERÍA 12V AGM ───┐
                          │      7Ah (±)          │
                          ├───────────────────────┤
                          │  + [Fusible 5A] - ─ ─ ├─────────────────┐
                          └───────────────────────┘                 │
                                   │                                │
                         (12V +)   │                            (GND -)
                                   │                                │
                        ┌──────────┴──────────┐                    │
                        │                     │                    │
                     ┌──┴──┐              ┌──┴──┐                 │
                     │LM2596           LED M1   │                 │
                     │ IN  OUT          LED M2  │                 │
                     │  |   |              │    │                 │
                     │ VIN VOUT          +12V  │                 │
                     │  |   |            Cátodo                  │
                     └──┬──┬─────────────────┘                   │
                        │  │         ↓                           │
                        │  │    IRF520N Drain                    │
                        │  │    (conectado a cátodo LED)         │
                        │  │         │                           │
                        │  │      ┌──┴───┐                       │
                        │  │      │ Gate │ ← Arduino D7          │
                        │  │      │      │   + 220Ω             │
                        │  │      └──┬───┘                       │
                        │  │         │                           │
                        │  │      ┌──┴───┐                       │
                        │  │      │Source│ ← GND                │
                        │  │      └──┬───┘                       │
                        │  │         │                           │
    (5V)  ────────→ Arduino ←─ COM ←┘  ← (GND común)
              D9→Servo      GND
              D7→Gate       5V

```

---

## Verificación de Conexiones Antes de Encender

- [ ] Fusible de 5A en línea de 12V positivo
- [ ] LM2596 entrada: +12V y GND conectados correctamente
- [ ] LM2596 salida: verificar 5V con multímetro antes de conectar Arduino
- [ ] Capacitores electrolíticos en LM2596 (entrada y salida): positivo hacia VOUT
- [ ] Arduino GND conectado a batería GND (masa común)
- [ ] Servo: 3 cables correctos (Signal a D9, VCC a 5V, GND a GND)
- [ ] MOSFET: Gate a D7 (vía 220Ω), Source a GND, Drain a LED(-)
- [ ] Pull-down 10kΩ en Gate del MOSFET
- [ ] LED módulos: (+) a 12V, (-) a Drain del MOSFET
- [ ] Sin cortos entre +12V, +5V y GND

---

## Notas Importantes

1. **LM2596 es un convertidor buck muy eficiente** (~92-95%)
2. **Disipación de calor**: con 2A de salida (10W) el chip se calienta pero no requiere disipador grande
3. **Ruido**: los capacitores de entrada y salida son críticos para evitar oscilaciones
4. **Protección contra inversión**: si conectas la batería al revés, el LM2596 puede dañarse
5. **Tierra común**: SIEMPRE conecta GND de batería, Arduino, servo y MOSFET al mismo punto

# Esquema de Conexiones - Antena Radar Prop

## Componentes principales
- **Batería**: 12V AGM 7Ah
- **Convertidor DC/DC**: LM2596 (12V → 5V)
- **Arduino**: Uno/Nano
- **Servo**: MG995
- **Módulos LED**: 2x módulos de 12V
- **MOSFET**: IRF520N (o mejor: IRLZ44N, IRLZ34N)
- **Resistencias**: 10kΩ (pull-down), 220Ω (gate)
- **Capacitores**: 100µF (entrada/salida LM2596), 100nF (bypass)
- **Fusibles**: 5A (entrada 12V)

---

## Diagrama Simple - RECOMENDADO

```
               +12V BATERÍA
                  |
                  |
       +----------+----------+
       |                     |
   LM2596 IN+             LED módulo (+)
       |                     |
   LM2596 IN- ------------- GND común
       |
   LM2596 OUT+ ----------+
                         |
                         ├------> Arduino 5V
                         |
                         └------> Servo VCC
       
   LM2596 OUT- ----------> Arduino GND
                              |
                              +------> Servo GND
                              |
                              +------> Servo Signal (D9)

Arduino D7 ---- 220Ω ---- Gate MOSFET
                          |
                         10kΩ
                          |
                         GND

MOSFET Source ------------ GND común
MOSFET Drain ------------ LED módulo (-)
```

---

## Tabla de Conexiones Pin a Pin

| De | A | Cable | Notas |
|---|---|-------|-------|
| Batería +12V | LM2596 VIN | Rojo 16AWG | Fusible 5A en el camino |
| Batería -12V | LM2596 GND | Negro 16AWG | GND común |
| Batería +12V | LED módulo (+) | Rojo 16AWG | Alimentación directa |
| LM2596 VOUT | Arduino 5V | Rojo 14AWG | Una sola línea |
| LM2596 VOUT | Servo VCC | Rojo 14AWG | Compartida con Arduino |
| LM2596 GND | Arduino GND | Negro 14AWG | Una sola línea |
| LM2596 GND | Servo GND | Negro 14AWG | Compartida con Arduino |
| Batería -12V | GND común | Negro 16AWG | Unión batería-Arduino-Servo |
| Arduino D9 | Servo Signal | Naranja 18AWG | Control PWM servo |
| Arduino D7 | 220Ω resistencia | Amarillo 18AWG | Gate MOSFET |
| 220Ω resistencia | MOSFET Gate | Amarillo 18AWG | Control gate |
| 10kΩ resistencia | MOSFET Gate | Naranja 18AWG | Pull-down |
| 10kΩ resistencia | GND común | Negro 18AWG | Retorno pull-down |
| MOSFET Source | GND común | Negro 16AWG | Retorno MOSFET |
| MOSFET Drain | LED módulo (-) | Negro 16AWG | Control LED |

---

## Diagrama Detallado por Bloques

### Bloque 1: Alimentación Principal (Batería → LM2596)

```
    BATERÍA 12V AGM
    
    (+) positivo
    │
    +---- Fusible 5A ────┬────────────────────────+
    │                    │                        │
    └────────────────────┼────────────────────────┤
                         │                        │
                     LM2596 VIN+              LED (+)
                     
    (-) negativo
    │
    ├─────────────────────────────────────────────+
    │                                             │
    └──── LM2596 GND ────────────────────── GND común
```

### Bloque 2: Conversión 5V (LM2596)

```
    LM2596 Buck Converter
    
    VIN (+) ──── 12V
    GND (-)  ──── GND común
    
              ┌─────────────────┐
              │ LM2596          │
              │                 │
    12V ──┤VIN+              │
          │ ADJ               │
          │ (ajuste con ────10kΩ/2.2kΩ)
    GND ──┤GND      VOUT+ ├─┬─ 5V común
              │                 │  para Arduino
              └─────────────────┘  y Servo
    
    VOUT- ──── GND común
```

### Bloque 3: Arduino + Servo (5V)

```
    Arduino UNO/NANO
    
    ┌──────────────────────┐
    │ ┌────────────────┐   │
    │ │   USB/GND/5V   │   │
    │ │                │   │
    │ │  GND  5V  D9 D7│   │
    │ │   |    |   |   |   │
    │ └───┼────┼───┼───┼───┘
    │     │    │   │   │
    │   GND   5V   │   │
    │     │    │   │   │
    └─────┼────┼───┼───┼───────
          │    │   │   │
          │    │   │   └─── 220Ω ───┐
          │    │   │                 │ Gate MOSFET
          │    │   │                 │
    ┌─────┼────┼───┼─────────────────┤
    │     │    │   │      Arduino    │
    │   GND   5V  D9                  │
    │     │    │   │                 │
    │   Servo Servo Servo            │
    │   GND  VCC Signal              │
    └─────────────────────────────────┘
          │    │   │
         GND  5V  Signal
               (PWM)
```

### Bloque 4: Control de LEDs (MOSFET + LEDs 12V)

```
    Arduino D7 ──── 220Ω ──── Gate
    
    IRF520N
    ┌──────────────────┐
    │                  │
    │  Gate ───────────┤─── De Arduino D7 (con 220Ω)
    │                  │
    │  Drain ──────────┤─── A LED módulo (-)
    │                  │
    │  Source ─────────┤─── A GND común
    │                  │
    └──────────────────┘
          │
         10kΩ (pull-down)
          │
         GND común
    
    LED módulo 12V
    
    (+) ──────────── +12V batería
    (-) ──────────── Drain MOSFET
```

---

## Orden de Conexión Recomendado

1. **Conecta la batería a LM2596**
   - Batería +12V → LM2596 VIN (con fusible 5A)
   - Batería GND → LM2596 GND

2. **Verifica salida de LM2596 con multímetro**
   - VOUT debe ser ~5V
   - Si no, ajusta potenciómetro LM2596

3. **Conecta Arduino y Servo a 5V**
   - LM2596 VOUT → Arduino 5V y Servo VCC (una sola línea)
   - LM2596 GND → Arduino GND y Servo GND (una sola línea)

4. **Conecta servo signal**
   - Arduino D9 → Servo Signal

5. **Conecta MOSFET y LEDs**
   - Arduino D7 → 220Ω → Gate MOSFET
   - 10kΩ pull-down entre Gate y GND
   - MOSFET Source → GND común
   - MOSFET Drain → LED (-)

6. **Conecta LED (+) a batería +12V**

7. **Verifica todas las conexiones antes de encender**

---

## Esquema ASCII Final Completo

```
                    ┌─── BATERÍA 12V AGM 7Ah ───┐
                    │          (+) y (-)         │
                    └───────────────────────────┘
                           │         │
                      Fusible 5A     │
                           │         │
                    ┌──────┴─────┐   │
                    │  LM2596    │   │
                    │ 12V→5V     │   │
                    │            │   │
            VIN+ ───┤            │   │
            GND  ───┤            │ LED (+)
            VOUT ───┼─────┬──────┘
                    │     │
                    │     ├──→ Arduino 5V
                    │     │
                    │     └──→ Servo VCC
                    │
            GND  ───┼─────┬──────→ Arduino GND
                    │     │
                    │     └──────→ Servo GND
                    │
                    └──────────────┬─────────────┐
                                   │             │
                            Arduino D9      Arduino D7
                                   │             │
                                   │          220Ω
                                   │             │
                            Servo Signal    Gate MOSFET
                                           │
                                          10kΩ (pull-down)
                                           │
                                           GND
                                               
                                      MOSFET Source → GND
                                      MOSFET Drain → LED (-)
```

---

## Configuración LM2596 para 5V

El LM2596 viene con un potenciómetro integrado. Pasos para ajustar:

1. Conecta multímetro entre VOUT y GND
2. Enciende la batería
3. Gira el potenciómetro del LM2596 hasta obtener 5.0V
4. Verifica que sea estable (sin oscilaciones)
5. Apaga y conecta Arduino

**Si usas resistencias fijas** (en lugar de potenciómetro):
- R1 = 10kΩ (entre VOUT y ADJ)
- R2 = 3.3kΩ (entre ADJ y GND)
- Resultado: ~5V

---

## Checklist Antes de Encender

- [ ] Batería desconectada
- [ ] Todas las soldaduras bien hechas
- [ ] No hay cortocircuitos entre +12V, +5V y GND
- [ ] Fusible de 5A instalado
- [ ] LM2596 VOUT verificado en 5V con multímetro
- [ ] Arduino GND y Batería GND conectados (masa común)
- [ ] Servo Signal en Arduino D9
- [ ] MOSFET Gate en Arduino D7 (con 220Ω)
- [ ] MOSFET Source en GND
- [ ] MOSFET Drain en LED (-)
- [ ] LED (+) en Batería +12V
- [ ] Pull-down 10kΩ en Gate MOSFET
- [ ] Código Arduino cargado y testeable

---

## Notas de Seguridad

1. **Siempre verifica LM2596 antes de conectar Arduino**
2. **Nunca toques componentes sin desconectar batería**
3. **Revisa voltajes con multímetro en cada paso**
4. **Si LM2596 calienta mucho, revisa configuración**
5. **Usa fusible adecuado (5A) en línea de 12V**
6. **Tierra común es crítica: une todos los GND en un punto**

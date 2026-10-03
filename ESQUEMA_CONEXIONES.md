# Esquema de Conexiones - Antena Radar Prop

## Concepto principal
Sí se puede usar una sola línea de GND común para:
- batería 12V
- Arduino
- Servo
- MOSFET
- LEDs

La clave es que todos compartan la misma masa, para que el circuito funcione bien y no haya ruido ni errores.

---

## Esquema final recomendado

```text
                    +12V BATERÍA AGM
                         |
                         |
                +--------+--------+
                |                 |
             FUSIBLE 5A         LED módulo (+)
                |                 |
                |               LED módulo (-)
                |                    |
                |                 MOSFET Drain
                |                    |
                +---------- LM2596 IN+
                             |
                        LM2596 IN-
                             |
                             +------------------ GND común

LM2596 OUT+ ──────────────► Arduino 5V
LM2596 OUT+ ──────────────► Servo VCC

LM2596 OUT- ──────────────► Arduino GND
LM2596 OUT- ──────────────► Servo GND
LM2596 OUT- ──────────────► MOSFET Source
LM2596 OUT- ──────────────► Batería 12V (-)

Arduino D9 ──────────────► Servo Signal

Arduino D7 ─── 220Ω ────► MOSFET Gate
MOSFET Gate ─── 10kΩ ───► GND común

MOSFET Drain ───────────► LED módulo (-)
LED módulo (+) ─────────► Batería +12V
```

---

## Versión más legible por bloques

```text
BATERÍA 12V
   +12V ─────► LM2596 IN+
   GND ─────► LM2596 IN-
   GND ─────► GND común
   +12V ─────► LED módulo (+)

LM2596 OUT+ ─────► Arduino 5V
LM2596 OUT+ ─────► Servo VCC

LM2596 OUT- ─────► Arduino GND
LM2596 OUT- ─────► Servo GND
LM2596 OUT- ─────► MOSFET Source
LM2596 OUT- ─────► GND común

Arduino D9 ─────► Servo Signal

Arduino D7 ───── 220Ω ───── MOSFET Gate
MOSFET Gate ─── 10kΩ ───► GND común

MOSFET Drain ─────► LED módulo (-)
```

---

## Conexiones exactas

### 1. Alimentación general
- Batería +12V → LM2596 IN+
- Batería -12V → LM2596 IN-
- Batería -12V → GND común
- LM2596 OUT+ → Arduino 5V
- LM2596 OUT+ → Servo VCC
- LM2596 OUT- → Arduino GND
- LM2596 OUT- → Servo GND
- LM2596 OUT- → MOSFET Source

### 2. Servo
- Arduino D9 → Servo Signal
- Servo VCC → 5V
- Servo GND → GND común

### 3. LEDs
- LED módulo (+) → Batería +12V
- LED módulo (-) → MOSFET Drain
- MOSFET Source → GND común
- Arduino D7 → 220Ω → MOSFET Gate
- 10kΩ → Gate a GND

---

## Observación importante sobre el MOSFET

El IRF520N es un MOSFET bastante viejo y no es muy eficiente con señales de 5V del Arduino. Mejor opción:
- IRLZ44N
- IRLZ34N
- FQP30N06L

Si usás IRF520N, puede funcionar, pero con menos eficiencia y más calentamiento.

---

## Resumen final

Sí, podés usar una sola línea de GND. De hecho, es la forma correcta.

La estructura final es:
- 12V de la batería alimenta LEDs y LM2596
- LM2596 convierte 12V a 5V
- 5V alimenta Arduino y Servo
- Un solo GND común conecta Arduino, servo, batería, MOSFET y LEDs

Eso es lo que debes hacer.

---

## Checklist final

- [ ] Batería +12V hacia LM2596 IN+
- [ ] Batería -12V hacia GND común
- [ ] LM2596 OUT+ hacia Arduino 5V
- [ ] LM2596 OUT+ hacia Servo VCC
- [ ] LM2596 OUT- hacia Arduino GND
- [ ] LM2596 OUT- hacia Servo GND
- [ ] LM2596 OUT- hacia MOSFET Source
- [ ] Arduino D9 → Servo Signal
- [ ] LED (+) → Batería +12V
- [ ] LED (-) → MOSFET Drain
- [ ] Arduino D7 → 220Ω → MOSFET Gate
- [ ] Gate → 10kΩ → GND
- [ ] Fusible 5A instalado antes del LM2596

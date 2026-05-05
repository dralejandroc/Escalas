# Clinimetrix — Sistema de Diseño de Escalas Interactivas

> Referencia técnica para replicar el formato estándar de `tdah-assessment.html` en nuevas escalas.

---

## 1. Estructura de página

```
body (fondo gradiente full-viewport)
  └── .card-wrap × N    ← una tarjeta por ítem + bienvenida + final + resultados
       ├── .card-header  ← gradiente + nombre escala + etiqueta de dominio
       └── .card-body    ← contenido de la tarjeta
```

- Solo una `.card-wrap` visible a la vez: `display: none` por defecto, `.active { display: block }`.
- La tarjeta de bienvenida arranca con clase `active`.

---

## 2. Variables de color por dominio

| Dominio | COLOR1 | COLOR2 | Uso |
|---------|--------|--------|-----|
| Estándar / Ansiedad / TDAH | `#29A98C` | `#112F33` | Fondo body, header, botones, interp-box |
| Depresión / Riesgo Suicida | `#c53030` | `#63171b` | Mismo esquema |
| HiTOP / Personalidad | `#553c9a` | `#2d1b69` | Mismo esquema |

Reemplazar COLOR1 y COLOR2 en todas las referencias al cambiar de dominio.

---

## 3. Estilos clave

### Body
```css
body {
    font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
    background: linear-gradient(135deg, COLOR1 0%, COLOR2 100%);
    min-height: 100vh;
    padding: 24px 16px;
}
```

### Tarjeta contenedor
```css
.card-wrap {
    max-width: 800px;
    margin: 0 auto 24px;
    background: #fff;
    border-radius: 20px;
    box-shadow: 0 8px 40px rgba(0,0,0,0.18);
    overflow: hidden;
    display: none;
}
.card-wrap.active { display: block; }
```

### Cabecera de tarjeta
```css
.card-header {
    background: linear-gradient(135deg, COLOR1, COLOR2);
    color: #fff;
    padding: 18px 28px;
    display: flex;
    justify-content: space-between;
    align-items: center;
}
.card-header .scale-name { font-size: 15px; font-weight: 600; opacity: .9; }
.card-header .q-label    { font-size: 13px; opacity: .8; }
```

### Cuerpo de tarjeta
```css
.card-body { padding: 28px 32px 24px; }
/* Mobile: */
@media (max-width: 600px) { .card-body { padding: 20px 18px 18px; } }
```

### Texto de pregunta
```css
.question-text {
    font-size: 22px;          /* Mobile: 18px */
    font-weight: 500;
    color: COLOR2;
    line-height: 1.5;
    margin-bottom: 28px;
}
```

### Opciones de respuesta
```css
.option {
    width: 100%;
    border: 2px solid #e2e8f0;
    border-radius: 12px;
    padding: 14px 20px;
    font-size: 14px;
    font-weight: 500;
    color: COLOR2;
    background: #fff;
    cursor: pointer;
    text-align: left;
    transition: all .2s;
}
.option:hover    { border-color: COLOR1; background: rgba(COLOR1_RGB, 0.06); }
.option.selected { border-color: COLOR1; background: rgba(COLOR1_RGB, 0.13); font-weight: 600; }
```

> Para COLOR1 `#29A98C` → RGB es `41,169,140`.  
> Para COLOR1 `#c53030` → RGB es `197,48,48`.

### Barra de progreso
```css
.q-progress-label { font-size: 11px; color: #a0aec0; margin-bottom: 4px; }
.progress-bar-wrap { height: 6px; background: #e2e8f0; border-radius: 3px; overflow: hidden; margin-bottom: 14px; }
.progress-bar-fill { height: 100%; background: linear-gradient(90deg, COLOR1, COLOR2); border-radius: 3px; transition: width .3s; }
```

Valor inline por ítem: `style="width: X%"` donde `X = (item_num / total_items) * 100`.

### Navegación inferior
```css
.bottom-nav { display: flex; justify-content: space-between; align-items: center; }
.btn-back {
    background: none;
    border: 1px solid #cbd5e0;
    border-radius: 8px;
    color: #718096;
    padding: 7px 16px;
    font-size: 13px;
    cursor: pointer;
}
.btn-back:hover { border-color: COLOR1; color: COLOR1; }
.item-number-badge { font-size: 11px; color: #cbd5e0; font-weight: 500; }
```

---

## 4. Tarjeta de bienvenida (card-0)

```css
.welcome-title { font-size: 22px; font-weight: 700; color: COLOR2; margin-bottom: 16px; }
.instructions-box {
    background: BG_TINT;          /* #f0faf7 (teal) | #fff5f5 (rojo) */
    border-left: 4px solid COLOR1;
    border-radius: 0 10px 10px 0;
    padding: 16px 18px;
    margin-bottom: 24px;
    color: COLOR2;
    font-size: 14px;
    line-height: 1.75;
}
.scale-preview {
    display: grid;
    grid-template-columns: repeat(N, 1fr);   /* N = número de opciones */
    gap: 6-8px;
    margin: 0 0 28px;
}
.preview-opt { border: 2px solid #e2e8f0; border-radius: 10px; padding: 10px; text-align: center; font-size: 11-13px; font-weight: 600; color: COLOR2; }
```

---

## 5. Tarjeta de resultados

```css
/* Número de puntuación — el COLOR se asigna dinámicamente */
.score-area { text-align: center; padding: 24px 20px 20px; border-radius: 16px; border: 2px solid #e2e8f0; margin-bottom: 20px; }
.score-num  { font-size: 72px; font-weight: 900; line-height: 1; /* color: JS */ }
.score-range { font-size: 14px; color: #a0aec0; margin-top: 2px; }
.score-badge { display: inline-block; margin-top: 10px; padding: 5px 16px; border-radius: 20px; font-size: 14px; font-weight: 700; /* color+bg+border: JS */ }

/* Subescalas */
.subscales-grid { display: grid; grid-template-columns: 1fr 1fr [1fr]; gap: 12px; margin-bottom: 20px; }
.subscale-box   { border: 1px solid #e2e8f0; border-radius: 10px; padding: 14px; text-align: center; }
.subscale-title { font-size: 12px; color: #718096; font-weight: 600; margin-bottom: 6px; }
.subscale-num   { font-size: 32px; font-weight: 700; /* color: JS */ }
.subscale-max   { font-size: 11px; color: #a0aec0; margin-top: 2px; }
.subscale-interp{ font-size: 11px; color: #718096; margin-top: 5px; }

/* Sección etiqueta */
.section-label { font-size: 11px; font-weight: 700; text-transform: uppercase; letter-spacing: .06em; color: #a0aec0; margin-bottom: 8px; }

/* Cuadro de interpretación */
.interp-box { background: #f7fafc; border-left: 4px solid COLOR1; border-radius: 0 10px 10px 0; padding: 14px 16px; margin-bottom: 16px; font-size: 13px; color: #2d3748; line-height: 1.7; }

/* Alertas */
.alerts-box { background: #fff5f5; border: 1px solid #feb2b2; border-radius: 10px; padding: 14px 16px; margin-bottom: 16px; font-size: 13px; color: #742a2a; }
```

---

## 6. Lógica JavaScript estándar

```javascript
const totalQuestions = N;
let currentCardId = 'card-0';
let responses = {};   // { qNum: value }

function showCard(id) {
    document.querySelectorAll('.card-wrap').forEach(c => c.classList.remove('active'));
    const key = (typeof id === 'number') ? 'card-' + id : id;
    document.getElementById(key).classList.add('active');
    currentCardId = key;
    window.scrollTo(0, 0);
    if (typeof id === 'number' && id >= 1 && id <= totalQuestions) restoreSelections(id);
}

function restoreSelections(n) {
    if (responses[n] === undefined) return;
    const card = document.getElementById('card-' + n);
    card.querySelectorAll('.option').forEach((btn, idx) => {
        btn.classList.toggle('selected', idx === responses[n]);
    });
}

// Para escalas tipo Likert (valor numérico):
function selectOption(qNum, value, btn) {
    btn.parentNode.querySelectorAll('.option').forEach(b => b.classList.remove('selected'));
    btn.classList.add('selected');
    responses[qNum] = value;
    setTimeout(() => {
        if (qNum < totalQuestions) showCard(qNum + 1);
        else showCard('card-final');
    }, 150);  // 150ms delay → auto-avance
}

// Para escalas True/False (valor string):
function selectOption(qNum, value, btn) {
    btn.parentNode.querySelectorAll('.option').forEach(b => b.classList.remove('selected'));
    btn.classList.add('selected');
    responses[qNum] = value;   // 'T' | 'F'
    setTimeout(() => {
        if (qNum < totalQuestions) showCard(qNum + 1);
        else showCard('card-final');
    }, 150);
}

function previousCard() {
    const match = currentCardId.match(/card-(\d+)/);
    if (!match) return;
    const n = parseInt(match[1]);
    if (n <= 1) showCard(0);
    else showCard(n - 1);
}
```

### Paleta semafórica de severidad

```javascript
// Colores estándar para niveles de severidad:
// Verde:   '#48bb78'  bg: 'rgba(72,187,120,0.12)'
// Amarillo:'#f6ad55'  bg: 'rgba(246,173,85,0.12)'
// Naranja: '#ed8936'  bg: 'rgba(237,137,54,0.12)'
// Rojo:    '#f56565'  bg: 'rgba(245,101,101,0.12)'
// Rojo oscuro: '#c53030' bg: 'rgba(197,48,48,0.12)'

function getLevel(score) {
    if (score < CUTOFF1) return { label:'Mínima',  color:'#48bb78', bgColor:'rgba(72,187,120,0.12)' };
    if (score < CUTOFF2) return { label:'Leve',    color:'#f6ad55', bgColor:'rgba(246,173,85,0.12)' };
    if (score < CUTOFF3) return { label:'Moderada',color:'#ed8936', bgColor:'rgba(237,137,54,0.12)' };
    return                    { label:'Grave',    color:'#f56565', bgColor:'rgba(245,101,101,0.12)' };
}
```

---

## 7. Checklist para nueva escala

- [ ] Copiar plantilla de `tdah-assessment.html`
- [ ] Sustituir COLOR1/COLOR2 según el dominio clínico (tabla §2)
- [ ] Sustituir `totalQuestions = N`
- [ ] Reemplazar `.q-label` en cada card-header con el nombre del dominio del ítem
- [ ] Ajustar `style="width: X%"` en cada barra de progreso (X = n/N×100)
- [ ] Actualizar la lógica de scoring en `calculateScore()`
- [ ] Completar `getLevel()` con los puntos de corte validados
- [ ] Agregar la escala al `index.html` en la categoría correcta

---

## 8. Escalas ya convertidas al estándar

| Escala | Archivo | Estado |
|--------|---------|--------|
| TDAH (ADHD-RS) | `tdah-assessment.html` | ✅ Referencia |
| BHS (Desesperanza de Beck) | `bhs-assessment.html` | ✅ Estandarizado |
| GADI (Ansiedad Generalizada) | `gadi-assessment.html` | ✅ Estandarizado |

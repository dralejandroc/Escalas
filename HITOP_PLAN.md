# HiTOP — Instrumento Completo de Apoyo al Clínico
## Plan técnico de construcción en 8 etapas

---

## Contexto del proyecto

Este documento es una hoja de ruta técnica para construir el **Sistema HiTOP de Apoyo Diagnóstico Dimensional**, un conjunto de escalas interconectadas más un navegador central que genera un perfil dimensional del paciente basado en el modelo **Hierarchical Taxonomy of Psychopathology (HiTOP)** de Kotov et al. (2017).

El sistema se construye en **8 etapas independientes**, cada una en una sesión de Claude distinta para gestionar el límite de tokens. Al inicio de cada sesión nueva, referenciar este archivo como contexto.

---

## Repositorio y estructura de archivos

**Repositorio GitHub:** `https://github.com/dralejandroc/Escalas`
**Rama de trabajo:** `main` (push directo tras merge desde worktree)
**URL pública (GitHub Pages):** `https://dralejandroc.github.io/Escalas/`

### Estructura de directorios del proyecto HiTOP

```
/consulta/                          ← raíz del repo
├── autoaplicadas/                  ← escalas existentes + nuevas escalas HiTOP
│   ├── phq15-assessment.html       ← ETAPA 1 (nueva)
│   ├── audit-assessment.html       ← ETAPA 1 (nueva)
│   ├── dudit-assessment.html       ← ETAPA 1 (nueva)
│   ├── cape15-assessment.html      ← ETAPA 2 (nueva)
│   ├── pid5brief-assessment.html   ← ETAPA 3 (nueva)
│   └── asrs-assessment.html        ← YA EXISTE — solo integrar, no recrear
├── heteroaplicadas/                ← escalas heteroaplicadas existentes
├── hitop/                          ← DIRECTORIO NUEVO para el Navigator
│   └── hitop-navigator.html        ← ETAPAS 4-6 (nueva herramienta central)
└── index.html                      ← modificar en ETAPA 7
```

> **Nota sobre worktrees:** Todo el trabajo nuevo debe realizarse en el worktree de Claude ubicado en:
> `/Users/alekscon/Documents/GitHub/consulta/.claude/worktrees/[nombre-worktree]/`
> Al finalizar cada etapa: `git add` → `git commit` → merge a main → `git push origin main`

---

## Estándares de diseño (aplicar en todas las etapas)

### Esquema de color HiTOP
El sistema HiTOP usa un esquema **púrpura/índigo** para diferenciarse visualmente de las escalas clinimétricas estándar (que usan verde teal):

```css
/* Gradiente principal HiTOP */
background: linear-gradient(135deg, #553c9a 0%, #2d1b69 100%);

/* Acento / selección */
border-color: #553c9a;
background: rgba(85,60,154,0.10);

/* Progress bar */
background: linear-gradient(90deg, #553c9a, #2d1b69);
```

### Formato unificado de cards (igual que resto de escalas)
- `.card-wrap`: max-width 800px, border-radius 20px, box-shadow 0 8px 40px rgba(0,0,0,0.18)
- `.card-header`: gradiente púrpura HiTOP
- `.card-body`: padding 28px 32px 24px
- Opciones: border 2px solid #e2e8f0, border-radius 12px. Seleccionado: border-color #553c9a, bg rgba(85,60,154,0.10)
- Progress bar: 6px, gradiente HiTOP
- Back button bottom-left + item badge bottom-right (color #cbd5e0)
- Auto-avance: `setTimeout(() => nextInOrder(), 150)` tras selección
- PDF export: `printWindow.document.write(html)` → `.close()` → `.print()` — NUNCA Blob

### Severidades estándar (colores)
```
#48bb78  ← Bajo / Ausente
#f6ad55  ← Moderado / Subclínico
#ed8936  ← Elevado / Clínico
#f56565  ← Grave / Muy elevado
#c53030  ← Muy grave / Crítico
```

---

## Nota sobre el ASRS (ya existente)

La escala **ASRS-v1.1** (Escala de Auto-reporte de TDAH en Adultos) **ya existe en el repositorio**. En la Etapa 7, cuando el Navigator necesite su score, usar el archivo existente. Confirmar el nombre exacto del archivo en `autoaplicadas/` al inicio de la Etapa 7 y asegurarse de que el enlace desde el Navigator apunte al archivo correcto.

---

## Flujo de datos entre escalas y Navigator

El HiTOP Navigator **no lee automáticamente** los resultados de las otras escalas. Funciona así:

1. El clínico aplica cada escala individualmente (el paciente las llena en sala de espera o el clínico las aplica)
2. El Navigator tiene **campos de entrada manual** donde el clínico ingresa los scores obtenidos
3. El Navigator convierte scores a niveles de severidad HiTOP (Ausente / Subclínico / Clínico / Grave)
4. Genera el perfil dimensional

Esto evita dependencias técnicas entre páginas y funciona sin backend.

### Mapeo de scores a espectros HiTOP

| Escala | Score → Nivel HiTOP |
|--------|---------------------|
| **PHQ-15** | 0-4=Ausente, 5-9=Subclínico, 10-14=Clínico, ≥15=Grave |
| **AUDIT** | 0-7=Ausente, 8-14=Subclínico, 15-19=Clínico, ≥20=Grave |
| **DUDIT** | 0-5=Ausente, 6-11=Subclínico, 12-24=Clínico, ≥25=Grave |
| **ASRS** | 0-3 ítems Part A=Ausente, 4 ítems=Subclínico, 5-6 ítems=Clínico |
| **CAPE-15 Positiva** | 0-0.5=Ausente, 0.51-1.0=Subclínico, 1.01-1.5=Clínico, >1.5=Grave |
| **CAPE-15 Negativa** | Igual que subescala positiva |
| **PID-5 Neg.Afecto** | 0-0.5=Ausente, 0.51-1=Subclínico, 1.01-2=Clínico, >2=Grave |
| **PID-5 Desapego** | Igual que Neg.Afecto |
| **PID-5 Antagonismo** | Igual que Neg.Afecto |
| **PID-5 Desinhibición** | Igual que Neg.Afecto |
| **PID-5 Psicoticismo** | Igual que Neg.Afecto |
| **PHQ-9** *(ya existe)* | 0-4=Ausente, 5-9=Subclínico, 10-19=Clínico, ≥20=Grave |
| **HARS** *(ya existe)* | 0-7=Ausente, 8-14=Subclínico, 15-23=Clínico, ≥24=Grave |
| **PCL-5** *(ya existe)* | 0-20=Ausente, 21-30=Subclínico, 31-49=Clínico, ≥50=Grave |

---

## ETAPA 1 — PHQ-15 + AUDIT + DUDIT
**Sesión independiente. Tokens estimados: ~24,000**

### Objetivo
Crear tres escalas autoaplicadas simples y agregarlas al índice bajo sus categorías correspondientes.

### PHQ-15 — Cuestionario de Síntomas Somáticos
- **Archivo:** `autoaplicadas/phq15-assessment.html`
- **Ítems:** 15 síntomas físicos, opciones 0/1/2 (En absoluto / Un poco / Mucho)
- **Cards:** Welcome → 3 cards de 5 ítems cada una → Resultados
- **Scoring:** Suma total 0-30. Cortes: <5=mínimo, 5-9=leve, 10-14=moderado, ≥15=grave
- **Subescalas a mostrar en resultados:** Dolor (ítems 1-4), Autonómico (ítems 5-9), Fatiga/Cognitivo (ítems 10-15)
- **Índice:** `autoaplicadas.suicidio` NO — agregar en `autoaplicadas.otros` hasta que exista sección HiTOP
- **Referencia:** Kroenke K, Spitzer RL, Williams JB. (2002). Arch Intern Med, 162(11), 1232-1237.

### AUDIT — Test de Identificación de Trastornos por Consumo de Alcohol
- **Archivo:** `autoaplicadas/audit-assessment.html`
- **Ítems:** 10 preguntas, opciones variables 0-4 por ítem (ítems 1-8) o 0/2/4 (ítems 9-10)
- **Cards:** Welcome → 5 cards de 2 ítems → Resultados
- **Scoring:** Suma 0-40. Cortes: 0-7=bajo riesgo, 8-14=consumo peligroso, 15-19=consumo perjudicial, ≥20=probable dependencia
- **Subescalas en resultados:** Consumo (ítems 1-3), Dependencia (ítems 4-6), Consecuencias (ítems 7-10)
- **Índice:** Crear categoría `sustancias` en autoaplicadas si no existe, o agregar a `otros`
- **Referencia:** Babor TF, et al. (2001). AUDIT: The Alcohol Use Disorders Identification Test. WHO.

### DUDIT — Test de Identificación de Trastornos por Uso de Drogas
- **Archivo:** `autoaplicadas/dudit-assessment.html`
- **Ítems:** 11 preguntas, opciones 0-4 (ítems 1-9) o 0/2/4 (ítems 10-11)
- **Cards:** Welcome → 3 cards de ~4 ítems → Resultados
- **Scoring:** Suma 0-44. Cortes: hombres ≥6 / mujeres ≥2 = uso problemático; ≥25 = probable dependencia
- **Incluir campo de sexo** en welcome card para aplicar corte correcto
- **Índice:** Misma categoría que AUDIT
- **Referencia:** Berman AH, et al. (2005). Eur Addict Res, 11(2), 103-110.

### Commit al final de Etapa 1
```bash
# En worktree:
git add autoaplicadas/phq15-assessment.html autoaplicadas/audit-assessment.html autoaplicadas/dudit-assessment.html
git commit -m "Add PHQ-15, AUDIT, DUDIT — HiTOP foundation scales (Etapa 1)"
# En /consulta main:
git add index.html
git commit -m "Register PHQ-15, AUDIT, DUDIT in index"
git merge --no-ff [worktree-branch] && git push origin main
```

---

## ETAPA 2 — CAPE-15
**Sesión independiente. Tokens estimados: ~14,000**

### Objetivo
Crear la escala CAPE-15 (Community Assessment of Psychic Experiences, versión breve de 15 ítems).

### CAPE-15 — Evaluación de Experiencias Psíquicas
- **Archivo:** `autoaplicadas/cape15-assessment.html`
- **Ítems:** 15 ítems seleccionados del CAPE-42 original con mayor carga factorial
- **Formato especial:** Cada ítem tiene DOS respuestas: Frecuencia (1-4) + Malestar causado (1-4)
  - Frecuencia: 1=Nunca / 2=A veces / 3=Seguido / 4=Casi siempre
  - Malestar: 1=No me molesta / 2=Un poco / 3=Bastante / 4=Mucho
- **Cards:** Welcome → 5 cards de 3 ítems (con los dos selectores por ítem) → Resultados
- **Subescalas:**
  - Positiva (psicosis subumbral): ítems 1-8 — alucinaciones, ideas de referencia, grandiosidad, suspiciosidad
  - Negativa (desapego): ítems 9-12 — anhedonia, embotamiento, pérdida de interés social
  - Depresiva: ítems 13-15 — desesperanza, pérdida de valor, tristeza persistente
- **Scoring:** Media de frecuencia por subescala (1-4). Punto de corte de elevación clínica: media ≥1.5
- **Resultados:** 3 subescalas mostradas con barra + nivel + malestar promedio
- **Índice:** Agregar en `autoaplicadas.trauma` o crear `autoaplicadas.psicosis` si corresponde
- **Referencia:** Capra C, et al. (2013). Schizophr Res, 149(1-3), 201-203. Konings M et al. (2006).

### Los 15 ítems del CAPE-15 (basados en validación)

**Subescala Positiva (ítems 1-8):**
1. ¿Tiene la sensación de que hay mensajes especiales para usted en lo que ve o escucha (TV, radio, periódicos)?
2. ¿Cree que ciertas personas no son lo que parecen ser?
3. ¿Siente que recibe mensajes especiales de alguna manera inusual?
4. ¿Ha tenido la experiencia de escuchar voces cuando está solo/a?
5. ¿Siente que algo está interfiriendo con su capacidad de pensar?
6. ¿Cree que puede influir en otras personas usando solo el pensamiento?
7. ¿Ha tenido la sensación de que personas a su alrededor no son reales?
8. ¿Siente que sus pensamientos están siendo controlados por algo externo a usted?

**Subescala Negativa (ítems 9-12):**
9. ¿Ha sentido que no le interesan las personas a su alrededor?
10. ¿Siente que es incapaz de experimentar emociones fuertes?
11. ¿Ha sentido que no hay nada que le produzca placer?
12. ¿Se ha sentido incapaz de iniciar cosas porque le parece que todo da igual?

**Subescala Depresiva (ítems 13-15):**
13. ¿Ha tenido la sensación de que no hay esperanza para el futuro?
14. ¿Ha sentido que no vale nada como persona?
15. ¿Ha tenido pensamientos de que sería mejor estar muerto/a?

### Commit al final de Etapa 2
```bash
git add autoaplicadas/cape15-assessment.html
git commit -m "Add CAPE-15 psychosis/detachment screener — HiTOP Etapa 2"
git merge && git push origin main
```

---

## ETAPA 3 — PID-5 Brief
**Sesión independiente. Tokens estimados: ~20,000**

### Objetivo
Crear el Inventario de Personalidad para DSM-5 — Versión Breve (25 ítems). Es la escala de mayor cobertura HiTOP: mapea directamente a 5 de los 6 espectros.

### PID-5 Brief — Inventario de Personalidad DSM-5 (versión breve)
- **Archivo:** `autoaplicadas/pid5brief-assessment.html`
- **Ítems:** 25 ítems (5 por dominio), opciones 0-3
  - 0 = Muy falso o a menudo falso
  - 1 = Algo falso o a veces falso
  - 2 = Algo verdadero o a veces verdadero
  - 3 = Muy verdadero o a menudo verdadero
- **Cards:** Welcome → 5 cards de 5 ítems (una por dominio, con nombre del dominio en header) → Resultados
- **5 Dominios y su mapeo HiTOP:**

| Dominio PID-5 | Espectro HiTOP | Facetas que mide |
|---------------|----------------|-----------------|
| **Afecto Negativo** | Internalizante | Labilidad emocional, ansiedad, inseguridad por separación |
| **Desapego** | Desapego | Retirada social, anhedonia, afecto restringido, evitación de intimidad |
| **Antagonismo** | Antagónico Ext. | Manipulativeness, engaño, grandiosidad, búsqueda de atención, insensibilidad |
| **Desinhibición** | Desinhibido Ext. | Irresponsabilidad, impulsividad, distractibilidad, toma de riesgos, perfeccionismo inverso |
| **Psicoticismo** | T. del Pensamiento | Creencias y experiencias inusuales, excentricidad, desregulación cognitivo-perceptual |

- **Scoring:** Media de los 5 ítems de cada dominio = 0.0 a 3.0. Elevación clínica sugerida: media ≥1.5
- **Resultados:** 5 barras de dominios con color por severidad + tabla + interpretación clínica por dominio
- **Índice:** Crear `autoaplicadas.personalidad` si no existe (verificar), agregar ahí
- **Referencia:** Krueger RF, et al. (2013). J Abnormal Psych. Instrumento oficial DSM-5, dominio público APA.

### Los 25 ítems del PID-5 Brief

**Dominio 1 — Afecto Negativo (ítems 1-5):**
1. Las emociones cambian mucho de un momento a otro y reacciono de una manera exagerada a las cosas.
2. Me preocupo mucho por muchas cosas.
3. Tengo miedos intensos que parecen tontos pero que no puedo controlar.
4. Me siento ansioso/a mucho tiempo.
5. Nada me parece divertido o interesante.

**Dominio 2 — Desapego (ítems 6-10):**
6. Prefiero estar solo/a que con otras personas.
7. No me importa mucho si lastimo los sentimientos de los demás.
8. No me entusiasma mucho hacer cosas.
9. Rara vez me emociono mucho con las cosas.
10. Me cuesta mucho conectarme emocionalmente con otras personas.

**Dominio 3 — Antagonismo (ítems 11-15):**
11. Uso a las personas para obtener lo que quiero.
12. Exagero mis logros para que la gente me admire más.
13. Es fácil para mí aprovecharme de los demás.
14. Soy más importante que la mayoría de la gente.
15. Me guío por mis sentimientos del momento sin pensar en las consecuencias.

**Dominio 4 — Desinhibición (ítems 16-20):**
16. Tomo decisiones impulsivamente.
17. Me cuesta mucho mantenerme al día con lo que tengo que hacer.
18. Digo las cosas sin pensar en cómo van a afectar a los demás.
19. Actúo sin pensar en lo que podría pasar.
20. Me meto en situaciones peligrosas aunque sé que son peligrosas.

**Dominio 5 — Psicoticismo (ítems 21-25):**
21. Creo que tengo poderes especiales que la mayoría de las personas no tienen.
22. Tengo experiencias de cosas, personas, o eventos que los demás no ven ni oyen.
23. Me pregunto si las personas que conozco son realmente quienes dicen ser.
24. Mi mente a veces piensa de maneras que son difíciles de explicar a otros.
25. Siento que hay fuerzas invisibles que afectan mis pensamientos o acciones.

### Commit al final de Etapa 3
```bash
git add autoaplicadas/pid5brief-assessment.html
git commit -m "Add PID-5 Brief (25 items, 5 HiTOP domains) — Etapa 3"
git merge && git push origin main
```

---

## ETAPA 4 — HiTOP Navigator: Estructura, Guía y Cards 0-3
**Sesión independiente. Tokens estimados: ~20,000**

### Objetivo
Crear el archivo `hitop/hitop-navigator.html` con la estructura completa, estilos, card de guía clínica, y las cards de entrada de scores para los primeros 3 espectros (Internalizante, Somatoforme, T. del Pensamiento).

### Especificaciones del Navigator

**Archivo:** `hitop/hitop-navigator.html`
**Tipo:** Heteroaplicada (badge CLÍNICO en cada header)
**Función:** Recibe scores de escalas ya aplicadas → genera perfil dimensional HiTOP

**Estructura general:**
```
card-0: Guía clínica + instrucciones de uso
card-1: Espectro Internalizante (inputs: PHQ-9, HARS, PCL-5, PAS)
card-2: Espectro Somatoforme (input: PHQ-15)
card-3: Espectro Trastorno del Pensamiento (inputs: CAPE-15 Positiva, PID-5 Psicoticismo)
card-4: Espectro Desapego (inputs: CAPE-15 Negativa, PID-5 Desapego)
card-5: Espectro Antagónico (input: PID-5 Antagonismo)
card-6: Espectro Desinhibido (inputs: AUDIT, DUDIT, ASRS, PID-5 Desinhibición)
card-7: Deterioro funcional (4 dominios: laboral, social, familiar, autocuidado) — selectores 0-4
card-results: Perfil dimensional completo con radar chart + diagnóstico presuntivo
```

### Diseño de cada card de espectro (cards 1-6)
Cada card de espectro tiene:
1. **Header** con nombre del espectro + badge CLÍNICO + color HiTOP
2. **Descripción clínica** del espectro (2-3 líneas en caja gris)
3. **Inputs de scores**: Para cada escala relevante:
   - Label: nombre de la escala
   - Input numérico pequeño (campo de texto, solo números)
   - Rango esperado mostrado debajo: "(0-27 · PHQ-9)"
   - Botón pequeño "→ Abrir escala" que abre la escala en nueva pestaña
4. **Síntesis automática**: Al ingresar scores, calcula nivel del espectro en tiempo real
5. **Indicador de espectro**: Badge con color que cambia automáticamente (Ausente/Subclínico/Clínico/Grave)

### Card 0 — Guía Clínica (contenido)
- Qué es HiTOP y en qué se diferencia del DSM
- Cómo usar el Navigator: primero aplicar escalas, luego ingresar scores aquí
- Tabla de los 6 espectros con descripción de 1 línea cada uno
- Nota de que el resultado es orientativo, no diagnóstico
- Lista de escalas del sistema con links a cada una

### Commit al final de Etapa 4
```bash
git add hitop/hitop-navigator.html
git commit -m "HiTOP Navigator: structure + guide + spectra cards 0-3 — Etapa 4"
git merge && git push origin main
```

---

## ETAPA 5 — HiTOP Navigator: Cards 4-7
**Sesión independiente. Tokens estimados: ~15,000**

### Objetivo
Completar las cards de los espectros restantes (Desapego, Antagónico, Desinhibido) y la card de deterioro funcional. Continuar desde el archivo `hitop/hitop-navigator.html` creado en Etapa 4.

**IMPORTANTE:** Al inicio de esta sesión, leer el archivo existente `hitop/hitop-navigator.html` para continuar desde donde quedó. No reescribir — agregar las secciones faltantes.

### Espectro Desapego (card-4)
- Inputs: CAPE-15 Subescala Negativa (media 1.0-4.0) + PID-5 Dominio Desapego (media 0.0-3.0)
- Síntomas centinela a mostrar como recordatorio clínico: aplanamiento afectivo, aislamiento, anhedonia, abulia

### Espectro Antagónico (card-5)
- Input principal: PID-5 Dominio Antagonismo (media 0.0-3.0)
- Síntomas centinela: manipulación, engaño, grandiosidad, falta de empatía, agresión
- Nota clínica: espectro más difícil de identificar sin instrumentos de personalidad

### Espectro Desinhibido (card-6)
- Inputs: AUDIT (0-40) + DUDIT (0-44) + ASRS Part A (0-6 ítems positivos) + PID-5 Desinhibición (0-3)
- Lógica: cualquier score positivo en AUDIT o DUDIT marca al menos Subclínico en este espectro
- Síntomas centinela: impulsividad, consumo de sustancias, conducta de riesgo, TDAH

### Deterioro Funcional (card-7)
- 4 dominios evaluados por el clínico, cada uno con selector 0-4:
  - Funcionamiento laboral/académico
  - Funcionamiento social y relaciones
  - Funcionamiento familiar
  - Autocuidado y actividades básicas
- Score total 0-16; contribuye al p-factor (factor general de psicopatología)
- No pertenece a ningún espectro específico pero modera la interpretación del perfil

### Commit al final de Etapa 5
```bash
git commit -m "HiTOP Navigator: spectra cards 4-7 + functional impairment — Etapa 5"
git push origin main
```

---

## ETAPA 6 — HiTOP Navigator: Resultados + Radar Chart
**Sesión independiente. Tokens estimados: ~20,000**

### Objetivo
Implementar la card de resultados del Navigator: perfil radar, p-factor, tabla dimensional, diagnóstico presuntivo y PDF.

**IMPORTANTE:** Leer `hitop/hitop-navigator.html` al inicio para continuar desde el estado actual.

### Radar Chart (Canvas HTML5 nativo — sin librerías externas)
```javascript
// Hexágono de 6 vértices para los 6 espectros
// Cada eje va de 0 (centro) a 3 (grave)
// Los ejes tienen etiquetas abreviadas:
// INT=Internalizante, SOM=Somatoforme, PEN=T.Pensamiento
// DES=Desapego, ANT=Antagónico, DIS=Desinhibido
// El polígono del perfil se rellena con color semitransparente según severidad máxima
```

### p-Factor estimado
- Se calcula como la media ponderada de los 6 espectros
- Se muestra como número 0.0-3.0 con nivel: Bajo / Moderado / Elevado / Muy elevado
- Interpretación: "Carga psicopatológica general estimada"

### Tabla de resultados dimensionales
Para cada espectro:
| Espectro | Nivel | Escalas contribuyentes | Acción sugerida |
|----------|-------|------------------------|-----------------|

### Diagnóstico Presuntivo HiTOP
Texto generado automáticamente según el perfil:
- Espectros primarios (nivel Clínico o Grave)
- Espectros secundarios (nivel Subclínico)
- Síndrome DSM más probable basado en combinación de espectros
- Instrumentos adicionales recomendados para profundizar

### Reglas de diagnóstico presuntivo (lógica JS)
```javascript
// Ejemplos de lógica:
// INT alto + SOM moderado → Trastorno Depresivo Mayor con síntomas somáticos
// INT alto (Fear subfactor) → Trastorno de Ansiedad
// PEN alto + DES alto → Espectro Esquizofrénico
// DIS alto + ANT alto → Trastorno de Personalidad Antisocial / ASPD
// ANT alto solo → Rasgos Narcisistas o Psicopatía subclínica
// DIS alto solo → TDAH / Trastorno por Sustancias
// INT alto + ANT moderado → Trastorno Límite de Personalidad (espectro)
```

### PDF del Navigator
El PDF exporta:
1. Nombre de sección "HiTOP — Perfil Dimensional del Paciente"
2. Fecha
3. Imagen del radar (canvas toDataURL)
4. Tabla de los 6 espectros con nivel y escalas
5. p-factor
6. Diagnóstico presuntivo HiTOP
7. Escalas completadas y scores ingresados
8. Nota: "Este perfil es orientativo. No sustituye el juicio clínico ni instrumentos diagnósticos validados."
9. Referencia: Kotov et al. (2017). J Abnormal Psych, 126(4), 454-477.

### Commit al final de Etapa 6
```bash
git commit -m "HiTOP Navigator: results card, radar chart, p-factor, presuntive Dx — Etapa 6"
git push origin main
```

---

## ETAPA 7 — Integración al índice (sección especial)
**Sesión independiente. Tokens estimados: ~8,000**

### Objetivo
Crear una sección especial en `index.html` para el sistema HiTOP, separada de autoaplicadas y heteroaplicadas.

### Estructura de la nueva sección en index.html

La sección HiTOP NO usa el toggle autoaplicadas/heteroaplicadas. Es una sección fija, siempre visible, ubicada **antes** del selector de escalas clinimétricas.

```html
<!-- SECCIÓN HITOP — antes del área de escalas clinimétricas -->
<div class="hitop-section">
  <div class="hitop-header">
    <div class="hitop-badge">BETA</div>
    <h2>Sistema HiTOP</h2>
    <p>Herramienta de Apoyo al Diagnóstico Dimensional</p>
    <p class="hitop-note">Creación propia basada en Kotov et al. (2017) — No es una escala estandarizada</p>
  </div>
  
  <div class="hitop-card" onclick="openScale('hitop/hitop-navigator.html')">
    <div class="hitop-card-icon">🧠</div>
    <div class="hitop-card-name">HiTOP Navigator</div>
    <div class="hitop-card-desc">Perfil dimensional de los 6 espectros</div>
  </div>
  
  <div class="hitop-scales-grid">
    <!-- Links a las escalas del sistema HiTOP -->
    <div>PHQ-15</div>
    <div>AUDIT</div>
    <div>DUDIT</div>
    <div>CAPE-15</div>
    <div>PID-5 Brief</div>
    <div>ASRS [link al existente]</div>
  </div>
</div>
```

### CSS de la sección HiTOP en index.html
```css
.hitop-section {
  background: linear-gradient(135deg, #553c9a15, #2d1b6910);
  border: 2px solid #553c9a40;
  border-radius: 16px;
  padding: 24px;
  margin-bottom: 32px;
}
.hitop-badge {
  display: inline-block;
  background: #553c9a;
  color: white;
  font-size: 10px;
  font-weight: 800;
  padding: 2px 8px;
  border-radius: 4px;
  letter-spacing: 1px;
  margin-bottom: 8px;
}
.hitop-note {
  font-size: 11px;
  color: #718096;
  font-style: italic;
  margin-top: 4px;
}
```

### También en Etapa 7: Registrar PHQ-15 / AUDIT / DUDIT en sus categorías
- PHQ-15 → agregar a `autoaplicadas.suicidio` o nueva categoría `autoaplicadas.somatoforme`
- AUDIT + DUDIT → agregar a `autoaplicadas.sustancias` (verificar si ya existe o crear)
- CAPE-15 → agregar a `autoaplicadas.esquizofrenia` o nueva categoría `autoaplicadas.psicosis`
- PID-5 Brief → agregar a `autoaplicadas.personalidad` (verificar si existe)

### Verificar enlace al ASRS existente
Al inicio de esta sesión, buscar el archivo ASRS existente:
```bash
ls autoaplicadas/ | grep -i asrs
```
Usar el nombre exacto para el link desde el Navigator y desde la sección HiTOP.

### Commit al final de Etapa 7
```bash
git add index.html
git commit -m "Add HiTOP special section to index + register all HiTOP scales — Etapa 7"
git push origin main
```

---

## ETAPA 8 — Revisión final, ASRS integration y control de calidad
**Sesión independiente. Tokens estimados: ~10,000**

### Objetivo
Verificar que todo el sistema funciona de punta a punta. Revisar el archivo ASRS existente e integrar su link al Navigator si no se hizo en etapas anteriores.

### Checklist de revisión

**Escalas nuevas:**
- [ ] PHQ-15: abre correctamente, score final correcto, PDF funciona
- [ ] AUDIT: corte diferencial funciona, subescalas en resultado
- [ ] DUDIT: campo de sexo aplica corte correcto
- [ ] CAPE-15: doble respuesta (frecuencia + malestar) por ítem, 3 subescalas en resultado
- [ ] PID-5 Brief: 5 dominios en resultado, colores de severidad correctos

**Navigator:**
- [ ] Los 6 espectros tienen inputs funcionales
- [ ] Cálculo de nivel por espectro es correcto (usar tabla de conversión de Etapa 0)
- [ ] Radar chart se dibuja correctamente con los 6 ejes
- [ ] p-factor calcula bien
- [ ] Diagnóstico presuntivo aparece para combinaciones comunes
- [ ] Botones "Abrir escala" llevan a la URL correcta
- [ ] Link al ASRS existente apunta al archivo real del repo
- [ ] PDF del Navigator incluye imagen del radar

**Índice:**
- [ ] Sección HiTOP visible antes del selector de tipo de escala
- [ ] Badge BETA visible
- [ ] Links del Navigator funcionan en GitHub Pages
- [ ] Escalas HiTOP también registradas en sus categorías clinimétricas

### Fixes conocidos anticipados
- Si el canvas del radar chart no aparece en PDF, usar imagen estática como fallback
- Si `hitop/` directory no existe en GitHub Pages, verificar que tiene `index.html` o usar ruta relativa
- Si ASRS tiene nombre de archivo con espacios o caracteres especiales, codificar en la URL

### Modificaciones futuras (registro para el clínico)
El Navigator tiene un sistema de versioning en el footer: `v1.0 · [fecha]`
Cada vez que se realice una modificación relevante, actualizar versión y fecha en el footer del Navigator.

### Commit final
```bash
git commit -m "HiTOP system v1.0: final QA pass, ASRS integration, all links verified — Etapa 8"
git push origin main
```

---

## Resumen ejecutivo del proyecto

| Etapa | Contenido | Tokens | Sesión |
|-------|-----------|--------|--------|
| 1 | PHQ-15 + AUDIT + DUDIT | ~24,000 | Nueva ventana |
| 2 | CAPE-15 | ~14,000 | Nueva ventana |
| 3 | PID-5 Brief (25 ítems) | ~20,000 | Nueva ventana |
| 4 | Navigator: estructura + cards 0-3 | ~20,000 | Nueva ventana |
| 5 | Navigator: cards 4-7 | ~15,000 | Nueva ventana |
| 6 | Navigator: resultados + radar chart | ~20,000 | Nueva ventana |
| 7 | Index integration + sección HiTOP | ~8,000 | Nueva ventana |
| 8 | QA final + ASRS integration | ~10,000 | Nueva ventana |
| | **TOTAL ESTIMADO** | **~131,000** | **8 sesiones** |

---

## Instrucciones para iniciar cada sesión

Al abrir una nueva ventana de Claude para cualquier etapa:

1. Compartir este archivo: `@/Users/alekscon/Documents/GitHub/consulta/HITOP_PLAN.md`
2. Decir: "Vamos a trabajar en la **Etapa N** del plan HiTOP. Lee el plan completo y ejecuta exactamente lo especificado para esa etapa."
3. Si es Etapa 5 en adelante, también compartir el archivo ya creado del Navigator: `@/Users/alekscon/Documents/GitHub/consulta/.claude/worktrees/[nombre]/hitop/hitop-navigator.html`

---

*Plan elaborado en base a: Kotov R, et al. (2017). J Abnorm Psychol, 126(4), 454-477 · Watson D, et al. (2022). World Psychiatry · Krueger RF, et al. (2021). World Psychiatry · Ruggero C, et al. (2019). Clin Psychol Sci Pract · Simms L, et al. (2022). Assessment*

*Sistema HiTOP Navigator — Creación propia. No es un instrumento psicométrico estandarizado. Uso clínico orientativo.*

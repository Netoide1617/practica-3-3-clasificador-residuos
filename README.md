# Práctica 3.3-P4 – Clasificador de residuos por foto con IA

## Datos Generales
| Campo | Detalle |
| :--- | :--- |
| **Asignatura** | Desarrollo Sustentable (ACD-0908) |
| **Carrera** | Ingeniería en Sistemas Computacionales (TecNM Campus Mazatlán) |
| **Unidad** | 3. Escenario socio-cultural |
| **Tema** | 3.3 Impacto de actividades humanas sobre la naturaleza |
| **Práctica** | 3.3-P4 Clasificador de residuos por foto |
| **Herramientas** | Telegram (`@BotFather`), Make (`Telegram Bot`, `Make AI Agent` con visión) |

---

## Arquitectura del Escenario en Make (`blueprint.json`)

El flujo automatizado implementado consta de 4 módulos conectados en Make y 1 filtro condicional:
1. **`Telegram Bot -> Watch Updates` (Recibir Foto):** Recibe las actualizaciones entrantes del bot `@ClasificadorResiduosEq_bot` mediante Webhook instantáneo.
2. **`Filtro [Photo Exists]`:** Evalúa la condición `Message -> Photo Exists` para procesar únicamente mensajes con fotografías y evitar consumo innecesario de créditos.
3. **`Telegram Bot -> Download a File` (Descargar Foto):** Descarga la fotografía enviada utilizando el identificador `{{last(map(1.message.photo; "file_id"))}}`.
4. **`Make AI Agent` (con visión):** Utiliza la conexión `Make's AI Provider` para analizar visualmente el residuo y responder en texto plano con su clasificación, reciclabilidad, contenedor y tiempo de degradación en el contexto de Mazatlán, Sinaloa.
5. **`Telegram Bot -> Send a Text Message or a Reply` (Responder al Usuario):** Envía la ficha técnica de salida al `Chat ID` del usuario en Telegram.

### Evidencias del Escenario y Prueba en Vivo
* **Escenario completo y ordenado con filtro visible:**
  ![Escenario Completo en Make](imagenes/escenario_completo_make.png)
* **Consumo de créditos y ejecución en Make (35 créditos / 1.19 MB):**
  ![Consumo de Créditos en Make](imagenes/consumo_creditos_make.png)
* **Prueba en vivo en Telegram (Botella PET Electrolit):**
  ![Prueba en Telegram](imagenes/prueba_bot_telegram_electrolit.png)

---

## Actividad 1. Investigación Previa

### 1.1 Clasificación de los residuos en México
De acuerdo con la **Ley General para la Prevención y Gestión Integral de los Residuos (LGPGIR)** y la **NOM-161-SEMARNAT-2011**, emitidas por la **SEMARNAT**, los residuos en México se agrupan en:
* **Residuos Orgánicos:** Desechos de origen biológico biodegradables (cáscaras de frutas y verduras, restos de alimentos, residuos de jardinería, asientos de café), aprovechables mediante compostaje o biodigestión.
* **Residuos Inorgánicos Reciclables:** Materiales industrializados que conservan valor técnico y comercial para reincorporarse a cadenas productivas si se acopian limpios y secos: PET, PEAD, latas de aluminio, metales ferrosos, vidrio, papel, cartón y envases multicapa (Tetrapak).
* **Residuos Inorgánicos No Reciclables (Sanitarios / Rechazo):** Materiales que por su mezcla de polímeros, degradación higiénica o falta de viabilidad técnica local no se reciclan (papel sanitario, colillas de cigarro, envolturas metalizadas, unicel contaminado, vasos de cartón plastificado).
* **Residuos de Manejo Especial (RME) y Residuos Peligrosos (RP):** Los RME (**NOM-161-SEMARNAT-2011**) abarcan desechos electrónicos (*e-waste*), llantas y residuos voluminosos; los RP (**NOM-052-SEMARNAT-2005**) presentan características **CRETIB** (Corrosivo, Reactivo, Explosivo, Tóxico, Inflamable o Biológico-Infeccioso), tales como pilas alcalinas, medicamentos caducos, aceites usados y químicos.
* *Fuentes oficiales:* SEMARNAT (2020), *Diagnóstico Básico para la Gestión Integral de los Residuos (DBGIR)*; LGPGIR (DOF); NOM-161-SEMARNAT-2011.

### 1.2 Recolección, separación y destino final en Mazatlán, Sinaloa
Mazatlán genera entre **650 y 800 toneladas diarias** de Residuos Sólidos Urbanos (incrementándose hasta un 30% en periodos turísticos como Carnaval, Semana Santa y verano). La recolección está a cargo de la **Dirección de Servicios Públicos Municipales (Aseo Urbano)** mediante camiones compactadores que operan de forma mixta (sin separación diferenciada domiciliaria en el camión). La recuperación de reciclables ocurre principalmente por acopio informal (pepena en ruta o sitio final) enfocada en aluminio, PET y cartón, además de recicladoras privadas y campañas municipales (*Reciclatón* y contenedores de pilas). Los residuos llegan al **Basurón Municipal de Mazatlán** (zona sur/Urías), en proceso regulatorio ante PROFEPA y el Ayuntamiento para transitar hacia estación de transferencia y relleno sanitario conforme a la **NOM-083-SEMARNAT-2003**.
* *Fuentes oficiales:* IMPLAN Mazatlán (*Diagnóstico Ambiental Municipal*); Dirección de Ecología y Medio Ambiente del Ayuntamiento de Mazatlán.

### 1.3 Tres datos sobre el impacto de los residuos en el mar y canales de la región
1. **Azolve e inundaciones en el Estero del Infiernillo y Arroyo Jabalines:** Durante la temporada de lluvias, decenas de toneladas de botellas PET, bolsas plásticas y unicel son arrastradas por los más de 60 km de canales pluviales hacia el Arroyo Jabalines y el Estero del Infiernillo, provocando taponamientos hidráulicos e inundaciones urbanas, además de asfixiar las raíces del manglar.
2. **Predominancia de plásticos y unicel en playas de Mazatlán:** Muestreos de basura marina realizados por ICMyL-UNAM Mazatlán, FACIMAR-UAS y asociaciones civiles como MazConCiencia registran que entre el **70% y el 85%** de los desechos recolectados en playas locales son plásticos de un solo uso (colillas de cigarro, fragmentos de unicel, taparroscas y botellas PET).
3. **Fragmentación en microplásticos e ingesta por fauna marina:** La radiación UV y el oleaje fragmentan el unicel y las bolsas plásticas en microplásticos (<5 mm), los cuales son ingeridos por peces comerciales, aves marinas y tortugas golfinas (*Lepidochelys olivacea*) que anidan en Mazatlán.
* *Fuentes oficiales:* Instituto de Ciencias del Mar y Limnología (ICMyL-UNAM Mazatlán); INECC; IMPLAN Mazatlán.

---

## Actividad 6. Pruebas con 10 Residuos y Verificación contra Fuentes Oficiales

| # | Residuo | Clasificación según la IA | ¿Correcta? | Tiempo según la IA | Tiempo según la fuente | Fuente |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **1** | Botella de suero (Electrolit PET) | Inorgánico reciclable | Sí | ~450 años | 100 a 1,000 años (~450-500 años) | SEMARNAT / INECC |
| **2** | Cáscara de fruta (plátano) | Orgánico | Sí | 2 a 10 semanas | 2 semanas a 6 meses | SEMARNAT (Guía de Residuos) |
| **3** | Lata de aluminio | Inorgánico reciclable | Sí | 200 a 500 años | 200 a 500 años | SEMARNAT / NOAA |
| **4** | Envase de unicel (EPS) | Inorgánico no reciclable | Sí | 500 a 1,000 años | 500 a 1,000+ años (se fragmenta) | INECC / Greenpeace México |
| **5** | Envase Tetrapak | Inorgánico reciclable | Sí | 30 años | 30 a 35 años | SEMARNAT / Tetra Pak México |
| **6** | Papel bond | Inorgánico reciclable | Sí | 2 a 5 meses | 2 a 5 meses (hasta 1 año) | SEMARNAT |
| **7** | Botella de vidrio | Inorgánico reciclable | Sí | 4,000 años | ~4,000 años | SEMARNAT |
| **8** | Pila alcalina AA | Manejo especial o peligroso | Sí | 500 a 1,000 años | 500 a 1,000 años | NOM-052-SEMARNAT / SEMARNAT |
| **9** | Bolsa de plástico (PEBD) | Inorgánico reciclable | Sí | 150 a 400 años | 150 a 400 años | SEMARNAT / PNUMA |
| **10** | Vaso de café desechable | Inorgánico reciclable | **No** (Es no reciclable) | 5 a 30 años | 30 años (por capa de polietileno) | SEMARNAT / SEDEMA |

* **Porcentaje de aciertos de la IA en la clasificación:** **90%** (9 de 10 aciertos).
* **Consumo de créditos en Make:** En las pruebas acumuladas del escenario se registraron **35 créditos consumidos y 1.19 MB de transferencia de datos** (un promedio de ~3.5 a 4 créditos por fotografía procesada).

---

## Preguntas de Reflexión

### 1. ¿Cuántos de los 10 residuos se pueden reciclar o compostar? ¿Cuántos de ellos separan realmente en su casa?
De los 10 residuos analizados en la práctica, técnicamente 7 tienen potencial directo de valorización: 1 es compostable (la cáscara de fruta) y 6 son reciclables (la botella PET de Electrolit, la lata de aluminio, el envase Tetrapak, el papel, la botella de vidrio y la bolsa de plástico limpia), mientras que la pila requiere acopio especial y tanto el unicel como el vaso de café plastificado terminan como rechazo no reciclable. Sin embargo, al contrastar este potencial técnico con la realidad de nuestros hogares en Mazatlán, normalmente solo separamos de manera constante entre 2 y 3 tipos de residuos, principalmente las botellas de PET y las latas de aluminio, y ocasionalmente las pilas cuando encontramos un contenedor específico. El resto de los materiales reciclables (como el papel, el vidrio o el Tetrapak) y los residuos orgánicos suelen depositarse mezclados en el mismo bote de basura doméstica. Esto ocurre tanto por falta de hábito y espacio en casa para tener múltiples contenedores, como por la percepción de que el camión recolector municipal de Aseo Urbano termina mezclando todas las bolsas al momento de compactarlas. Esta brecha demuestra que más del 60% de los residuos que podrían reintegrarse a la economía circular o convertirse en composta terminan enterrados o expuestos en el basurón municipal.

### 2. ¿Qué residuo les sorprendió más por su tiempo de degradación? ¿Qué impacto tiene cuando llega al mar o a un canal de Mazatlán?
El residuo que más nos sorprendió fue el envase de unicel (poliestireno expandido), junto con la botella PET (~450 años) y la pila alcalina, ya que tardan entre 450 y más de 1,000 años en degradarse a pesar de ser envases que se utilizan apenas unos minutos para consumir una bebida o transportar comida rápida. En una ciudad costera como Mazatlán, cuando las botellas PET, el unicel o las bolsas plásticas son arrojados en la vía pública o mal dispuestos, las lluvias de verano los arrastran directamente hacia los canales pluviales, el Arroyo Jabalines y el Estero del Infiernillo. En primera instancia, estos residuos flotan y generan tapones hidráulicos severos en las alcantarillas y puentes, agravando las inundaciones en colonias enteras de la ciudad. Posteriormente, al llegar al estero, a los manglares o a las playas de la bahía mazatleca, los polímeros no se biodegradan, sino que por acción del sol y el oleaje se fragmentan en miles de partículas de microplástico. Estas partículas absorben contaminantes químicos del agua y son confundidas con alimento por peces, aves marinas, cangrejos y tortugas marinas, causando bloqueos digestivos letales en la fauna y contaminando los recursos pesqueros que consumimos localmente.

### 3. ¿En qué casos se equivocó la IA? ¿A qué creen que se debió (foto, material mezclado, falta de contexto local)?
La inteligencia artificial tuvo una precisión sobresaliente del 90% al identificar objetos claros como la botella de plástico PET de Electrolit (donde correctamente indicó enjuagarla y aplastarla ligeramente para facilitar su reciclaje), latas o cáscaras de fruta, pero se equivocó en residuos compuestos como el vaso de café desechable. En el caso del vaso de café, la visión por computadora identificó la textura exterior como cartón y lo clasificó erróneamente como "inorgánico reciclable", sin detectar que su interior tiene una película plástica adherida de polietileno que hace inviable su reciclaje convencional. Asimismo, la IA tiende a responder con criterios ideales internacionales (asumiendo que existen contenedores de reciclaje diferenciados en cada esquina o plantas recicladoras para todos los polímeros), lo cual evidencia una falta de contexto local sobre la infraestructura real de recolección en Mazatlán. Otro factor determinante es la iluminación y el ángulo de la fotografía: cuando un envase está aplastado, sucio o mal enfocado, el modelo visual pierde certeza sobre el material principal. Esto nos enseña como ingenieros en sistemas que los modelos de IA requieren ingeniería de prompts contextualizada y validación contra normas oficiales antes de tomar sus salidas como verdades absolutas.

### 4. ¿De quién es la responsabilidad de que un residuo se recicle: del ciudadano, del municipio o de la empresa que lo fabrica?
Desde el enfoque del desarrollo sustentable y de la Ley General para la Prevención y Gestión Integral de los Residuos (LGPGIR), la responsabilidad no recae en un solo actor, sino que es una responsabilidad compartida pero diferenciada entre los tres sectores. En primer lugar, las empresas fabricantes tienen una responsabilidad de origen fundamental (conocida como Responsabilidad Extendida del Productor), ya que son quienes diseñan envases de un solo uso o materiales difíciles de reciclar como el unicel y los empaques multicapa, por lo que deberían rediseñar sus productos hacia materiales retornables o biodegradables y financiar cadenas de acopio. En segundo lugar, el gobierno municipal de Mazatlán tiene la obligación estructural de modernizar el sistema de recolección, transitar definitivamente del basurón a cielo abierto hacia un relleno sanitario normado con planta de separación, e instalar infraestructura accesible de acopio diferenciado en las colonias. Finalmente, el ciudadano tiene la responsabilidad directa en el consumo diario y en el primer eslabón de la cadena: reducir la compra de productos desechables, entregar los residuos limpios, enjuagados y separados, y no arrojar desechos ni pilas a la basura común o a la calle. Si cualquiera de estos tres eslabones falla, el ciclo del reciclaje se rompe por completo.

### 5. ¿Qué cambio concreto harían en su consumo después de esta práctica?
Después de realizar esta práctica y comprobar con fuentes oficiales que materiales que usamos por unos minutos tardan entre 450 y 1,000 años en degradarse en el ecosistema de Mazatlán, el cambio más concreto e inmediato que aplicaremos como equipo es eliminar el uso de envases de unicel y reducir la compra de bebidas embotelladas de un solo uso dentro de nuestra rutina diaria en el campus del TecNM. En su lugar, llevaremos de forma permanente un cilindro o termo reutilizable para agua y café, así como recipientes lavables al comprar alimentos preparados fuera de casa, atacando el problema desde la prevención (rechazar y reducir) antes de siquiera generar el residuo. En segundo lugar, cuando consumamos algún envase de PET (como las botellas de suero o agua), aplicaremos el consejo técnico del bot: enjuagarlas, aplastarlas y acopiarlas por separado junto con las latas de aluminio y el cartón limpio para entregarlos a centros de reciclaje locales, además de separar las pilas usadas en un contenedor especial. Finalmente, desde nuestra formación en Ingeniería en Sistemas Computacionales, aplicaremos principios de tecnología sustentable (*Green IT*), diseñando automatizaciones eficientes que optimicen el consumo de cómputo y aporten soluciones ambientales a nuestra comunidad.

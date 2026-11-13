# UNIDAD-3-BASE-DE-DATOS-EJERCICIO-9
El diagrama muestra un **Modelo Relacional (MR)** orientado a la gestión de una plataforma de aprendizaje en línea (*e-learning*), compuesto por **9 tablas interconectadas** que administran instructores, cursos, módulos, lecciones, estudiantes, inscripciones y evaluaciones.

### 1. Tablas y Estructura

* **INSTRUCTOR**
* `ID_INSTRUCTOR` (PK)
* `NOMBRE`
* `BIOGRAFIA`
* `ESPECIALIDAD`
* `DATOS_CONTACTO`


* **CURSO**
* `ID_CURSO` (PK)
* `TITULO`
* `DURACION`
* `PRECIO`
* `NIVEL_DIFICULTAD`


* **MODULO**
* `ID_MODULO` (PK)
* `TITULO`
* `NUMERO_ORDEN`
* `ID_CURSO` (FK) — Relacionado con `CURSO`


* **LECCION**
* `ID_LECCION` (PK)
* `TITULO`
* `CONTENIDO`
* `DURACION`
* `TIPO`
* `ID_MODULO` (FK) — Relacionado con `MODULO`


* **ESTUDIANTE**
* `ID_ESTUDIANTE` (PK)
* `TELEFONO`
* `NOMBRE`
* `DNI`
* `CORREO`


* **INSCRIPCION** *(Tabla intermedia / asociativa entre Estudiante y Curso)*
* `ID_INSCRIPCION` (PK)
* `FECHA`
* `ID_ESTUDIANTE` (FK) — Relacionado con `ESTUDIANTE`
* `ID_CURSO` (FK) — Relacionado con `CURSO`


* **INTENTO_RESOLUCION**
* `ID_INTENTO` (PK)
* `FECHA_INTENTO`
* `CALIFICACION`
* `ESTADO`
* `ID_ESTUDIANTE` (PK / FK) — Relacionado con `ESTUDIANTE`
* `ID_EVALUACION` (PK / FK) — Relacionado con `EVALUACION`
* `ID_CURSO` (FK)


* **EVALUACION**
* `ID_EVALUACION` (PK)
* `TIPO`
* `ID_CURSO` (FK) — Relacionado con `CURSO`


* **PREGUNTAS**
* `ID_PREGUNTA` (PK)
* `ENUNCIADO`
* `OPCIONES`
* `RESPUESTA_CORRECTA`





---

### 2. Relaciones Principales

* **Instructor y Curso:** Vincula a los docentes responsables con los cursos que imparten dentro de la plataforma.
* **Curso, Módulo y Lección (Jerarquía de Contenidos):** Un curso se divide en múltiples módulos ordenados secuencialmente (`MODULO` mediante `ID_CURSO`), y cada módulo contiene varias lecciones específicas con su material y duración (`LECCION` mediante `ID_MODULO`).
* **Estudiante e Inscripción:** La tabla asociativa `INSCRIPCION` resuelve la relación de muchos a muchos entre los alumnos (`ESTUDIANTE`) y la oferta educativa (`CURSO`), registrando la fecha en que un estudiante se matricula en un curso determinado.
* **Evaluación y Preguntas:** Cada curso cuenta con sus propias instancias evaluativas (`EVALUACION` vinculada por `ID_CURSO`), las cuales se componen de un banco de ítems registrados en `PREGUNTAS`.
* **Estudiante, Evaluación e Intento de Resolución:** La tabla `INTENTO_RESOLUCION` registra cada vez que un estudiante rinde una evaluación, almacenando la fecha del intento, la calificación obtenida y el estado de aprobación.

* El diagrama muestra el proceso completo de **normalización de bases de datos** aplicado de manera secuencial —desde una tabla inicial sin normalizar hasta alcanzar la Tercera Forma Normal (3NF)— para una plataforma educativa de aprendizaje en línea (*e-learning*) que gestiona instructores, cursos, módulos, lecciones, estudiantes, inscripciones, evaluaciones y preguntas.

A continuación, se detalla lo que se realiza en cada fase representada en el esquema:

---

### 1. Fase Normal N°0 (Tabla Desnormalizada)

* **Estado inicial:** Se parte de una **única tabla grande y plana** donde cada fila corresponde a un estudiante y agrupa atributos multivaluados y estructuras anidadas dentro de una misma celda (datos combinados de estudiante/DNI, listas de cursos con precio y nivel, instructores asignados, jerarquías anidadas de módulos/lecciones/evaluaciones/preguntas e historial de intentos con sus notas y estados).
* **Problema:** Presenta grupos repetitivos anidados y una severa falta de atomicidad, lo que impide realizar consultas relacionales estándar y provoca anomalías operativas de inserción, actualización y borrado.



### 2. Primera Forma Normal (1NF)

* **Condición:** Se exige que todos los atributos contengan **valores atómicos** (indivisibles, un único dato por celda) y que cada tabla disponga de una clave primaria identificativa.
* **Proceso aplicado:** Se eliminan los grupos repetitivos desglosando los registros en filas tabulares individuales y separando el banco de preguntas:
* **TABLA PLANILLA:** Aplana la información operativa vinculando al estudiante (`ID_ESTUDIANTE`), el curso (`ID_CURSO`) y cada intento realizado (`ID_INTENTO`) como clave compuesta, junto con los datos del curso, instructor, fecha, calificación y estado.
* **TABLA PREGUNTAS:** Extrae como una tabla independiente los ítems de los exámenes (`ID_PREGUNTA` como PK e `ID_EVALUACION` como FK), detallando el enunciado, las opciones y la respuesta correcta.





### 3. Segunda Forma Normal (2NF)

* **Condición:** La base de datos debe estar en 1NF y **todos los atributos que no forman parte de la clave deben depender por completo de toda la clave primaria** (eliminando las dependencias parciales generadas por la clave compuesta de la etapa anterior).
* **Proceso aplicado:** Se separan las entidades base según la clave exacta de la que dependen:
* **TABLA ESTUDIANTE:** Aísla los datos personales que dependen exclusivamente de `ID_ESTUDIANTE` (teléfono, nombre, DNI y correo).
* **TABLA CURSO:** Agrupa los atributos propios de cada materia (`ID_CURSO` como PK, título, duración, precio y nivel de dificultad), manteniendo aún temporalmente los datos del instructor.
* **TABLA INSCRIPCION:** Formaliza la matrícula del estudiante en el curso (`ID_INSCRIPCION` como PK, fecha, `ID_ESTUDIANTE` e `ID_CURSO`).
* **TABLA INTENTO_RESOLUCION:** Separa el registro de las rendiciones (`ID_INTENTO` como PK, fecha del intento, calificación y estado) vinculándolo con el estudiante, la evaluación y el curso.





### 4. Tercera Forma Normal (3NF)

* **Condición:** Debe estar en 2NF y **carecer de dependencias transitivas** (ningún atributo no clave debe depender de otro atributo que tampoco sea clave; todo atributo no clave debe depender estrictamente de la clave primaria).
* **Proceso aplicado:** Se termina de modularizar y aislar por completo todas las entidades independientes y jerarquías de contenido de la plataforma, conformando las **9 tablas definitivas**:
* **TABLA INSTRUCTOR:** Separa los datos de los docentes (`ID_INSTRUCTOR` como PK, nombre, biografía, especialidad y datos de contacto), eliminando la dependencia transitiva que existía dentro de `CURSO`.
* **TABLA CURSO, TABLA MODULO y TABLA LECCION:** Desglosan jerárquicamente la estructura académica de cada curso en sus módulos ordenados y sus respectivas lecciones individuales.
* **TABLA ESTUDIANTE e INSCRIPCION:** Mantienen de forma limpia el padrón de alumnos y sus matrículas a los cursos.
* **TABLA EVALUACION, TABLA PREGUNTAS y TABLA INTENTO_RESOLUCION:** Estructuran de manera independiente las instancias evaluativas por curso, el banco de preguntas asociado y el historial de intentos y calificaciones de cada estudiante.
<img width="1512" height="1272" alt="EJERCICIO 9-MR drawio" src="https://github.com/user-attachments/assets/10f1ca4d-bb3e-484e-9134-73db78062c97" />

![Uploading EJERCICIO 9-FASES DE NORMALIZACION.drawio.png…]()


**Misión del Proyecto**

Transformar el piso de manufactura en un entorno inteligente, predictivo y basado en datos, donde la alta rotación de personal deje de ser un cuello de botella para convertirse en una oportunidad de desarrollo mediante la asignación optimizada de talento, el control de calidad en tiempo real y el reconocimiento al desempeño.

**Pitch Elevator**  
En la industria maquiladora, la alta rotación y la falta de visibilidad en línea provocan cuellos de botella, retrabajos costosos y entregas tardías. SG-Prod es un tablero de mando gerencial que resuelve este problema desde el origen: optimiza la asignación de personal antes de iniciar el turno mediante algoritmos de matriz de habilidades, permite a los jefes de línea registrar calidad hora por hora y convierte esos datos en decisiones automáticas.  
Con SG-Prod, la gerencia no solo reduce el Scrap y el Retrabajo, sino que incentiva a sus mejores operadores con bonos automáticos de cero defectos y detecta exactamente en qué estación se requiere capacitación dirigida.

|  |  |  |  |  |  |
| :---- | :---- | :---- | :---- | :---- | :---- |
|  |  |  |  |  |  |
|  |  |  |  |  |  |
|  |  |  |  |  |  |
|  |  |  |  |  |  |
|  |  |  |  |  |  |

### **1\. Gestión de Información y Alineación (Transversal)**

* **Encabeza:** Rocío  
* **Lo que hace:**  
  * Recolecta requerimientos, reglas de negocio y datos operativos.  
  * Mantiene alineados a todos los microequipos con la visión estratégica (bonos, capacitación y simulación).

### **2\. Diseño UI/UX e Identidad Visual (Transversal)**

* **Encabeza:** Martha  
* **Acompañante:** Eduardo  
* **Lo que hacen:**  
  * **Marthita:** Lidera la línea gráfica, paleta de colores, tipografías y maquetación base para todo el proyecto.  
  * **Eduardo:** Colabora en la creación de vistas y prototipos para que el Módulo 1 y el Módulo 2 mantengan exactamente el mismo estilo visual.  
  * Aseguran la usabilidad de las pantallas para el personal de planta (captura ágil y visualización clara de gráficos).

### **3\. Base de Datos (Transversal)**

* **Encabeza:** Ángela  
* **Acompañante:** Karen  
* **Lo que hacen:**  
  * Diseñan el Modelo Entidad-Relación (MER) para todo el sistema.  
  * Definen los modelos en Django (`models.py`) con sus relaciones, restricciones e índices.  
  * Generan las vistas para cálculo de métricas y datos de prueba (`seeding`).

### **4\. Módulo 1: Matriz de Habilidades y Asignación**

* **Encabeza:** Mariana  
* **Acompañante:** Martha *(en desarrollo funcional)*  
* **Lo que hacen:**  
  * Configuran y personalizan el Admin de Django para la gestión de empleados, estaciones y habilidades.  
  * Desarrollan la pantalla de asignación de personal a las líneas de producción según el perfil requerido.  
  * Crean la consulta e historial de experiencia y capacitación por empleado.

### **5\. Módulo 2: Control de Calidad y KPIs**

* **Encabeza:** Iván  
* **Acompañante:** Eduardo *(en desarrollo funcional)*  
* **Lo que hacen:**  
  * Desarrollan la lógica y backend de la captura rápida de piezas (aceptadas, rechazadas y retrabajo).  
  * Conectan y alimentan los gráficos del Dashboard Gerencial (% FPY, Piezas/Hora y Tasa de Defectos).  
  * Implementan la regla de negocio para el cálculo de bonos por cero defectos.

# **MVP**

## **A. Requerimientos Funcionales (RF)**

### **Módulo 1: Matriz de Habilidades y Asignación de Estaciones**

* **RF-1.1 Catálogo de Empleados y Estaciones:** CRUD de empleados (número de empleado, nombre, estado) y estaciones de trabajo por línea.  
* **RF-1.2 Matriz de Habilidades (Skill Matrix):** Mapeo de competencia de cada empleado por estación utilizando los niveles: Principiante, Autónomo, Experto, e Instructor.  
* **RF-1.3 Motor de Sugerencia y Validación de Asignación:**  
  * El sistema **sugiere** la distribución ideal de personal para una línea basándose en la matriz de habilidades.  
  * El sistema **permite** asignar operadores Principiantes, pero emite una **alerta visual** para que el jefe de línea supervise la estación o le asigne un Instructor de apoyo.  
* **RF-1.4 Extensibilidad de Asistencia:** Acepta por defecto la presencia de todo el personal activo, dejando los ganchos de código (*hooks*) preparados para integrar módulos de control de asistencia en el futuro.

### **Módulo 2: Control de Calidad y Registro Operativo**

* **RF-2.1 Captura Hora por Hora:** Interfaz optimizada para que el Jefe de Línea registre cada 60 minutos el volumen de piezas por estación: *Aceptadas*, *Rechazadas* y *Retrabajo*.  
* **RF-2.2 Catálogo Estandarizado de Defectos:** Selección obligatoria de motivos de rechazo/retrabajo según el catálogo técnico precargado.

### **Módulo 3: Dashboard Gerencial, KPIs e Incentivos**

* **RF-3.1 Visualización "At-A-Glance":** Tablero gerencial con semáforos de calidad (Verde/Amarillo/Rojo) y tendencias hora por hora.  
* **RF-3.2 KPIs Clave:**  
  * **First Pass Yield (FPY)** por estación, línea y turno.  
  * **Piezas/Hora vs. Meta**.  
  * **Tasa de Defectos por Estación y Empleado**.  
* **RF-3.3 Regla de Bonos e Incentivos:** Identificación automática de operadores con cero defectos en el periodo evaluado para asignación de bonos de productividad.  
* **RF-3.4 Alertas de Capacitación Dirigida:** Reporte automático de las estaciones con mayor índice de rechazo para programar entrenamientos específicos.

## **B. Requerimientos No Funcionales (RNF)**

* **RNF-1 Autenticación Rápida (Planta):** Login mediante escaneo de Código QR (que codifica el N° de Empleado \+ PIN de 6 dígitos) o entrada manual.  
* **RNF-2 Rendimiento sobre Wi-Fi:** Interfaz ultra-ligera optimizada para redes Wi-Fi de planta, asegurando tiempos de respuesta menores a 1.5 segundos en la captura horaria.  
* **RNF-3 Arquitectura Ampliable:** Backend en Django con modelos normalizados y capas decoupling para permitir la ingesta masiva manual (captura MVP) o futura integración mediante APIs hacia un ERP/MES.  
* **RNF-4 Usabilidad Gerencial:** Arquitectura visual plana (máximo 2 clics para cualquier métrica relevante) sin tablas extensas sin procesar.

## **C. Plan de Ejecución (Cronograma de 10 Semanas / 5 Sprints)**

| Sprint | Enfoque Principal | Entregables Clave |
| :---- | :---- | :---- |
| **Sprint 1 (Sem 1-2)** | **Bases & Autenticación** | Entorno Django/PostgreSQL, Modelos base de datos, Login por QR/PIN, Django Admin configurado. |
| **Sprint 2 (Sem 3-4)** | **Módulo 1: Skill Matrix** | CRUD Empleados/Estaciones, Matriz de Habilidades, Algoritmo de sugerencia y alertas de asignación. |
| **Sprint 3 (Sem 5-6)** | **Módulo 2: Captura Horaria** | UI/UX para el Jefe de Línea, captura hora por hora, catálogo de defectos y registro de piezas. |
| **Sprint 4 (Sem 7-8)** | **Módulo 3: Dashboard & Bonos** | Motor de cálculo de FPY y Defectos, Tablero Gerencial "At-a-Glance", reporte de bonos y capacitación. |
| **Sprint 5 (Sem 9-10)** | **Hardening & Demo** | Carga de datos reales del caso de estudio, pruebas E2E, ajustes de UI, documentación y presentación final. |


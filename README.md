📊 HelpDeskBot – Módulo de Reportes
📌 Descripción

Este proyecto extiende la funcionalidad de HelpDeskBot incorporando un sistema de reportes que permite visualizar directamente desde Telegram el uso del sistema, específicamente:

Tipos de solicitudes registradas

Estado de cada solicitud

Resumen general del sistema

Todo esto sin necesidad de endpoints externos ni archivos adicionales, utilizando únicamente los datos almacenados en Google Sheets.

⚙️ Fuente de Datos

El reporte se genera a partir de la hoja:

SOLICITUDES

Campos utilizados:

tipo

estado

id_ticket

descripcion

fecha_creacion

Adicionalmente:

LOGS → para contar interacciones con el bot

🧠 Lógica del Reporte
🔹 1. Agrupación por tipo

Se agrupan las solicitudes por el campo tipo y se calculan:

total_solicitudes

abiertas

en_proceso

cerradas

🔹 2. Resumen general

Se calculan los siguientes indicadores:

Tipo más frecuente → el tipo con mayor número de solicitudes

Total de solicitudes registradas → total de filas en SOLICITUDES

Total de interacciones del bot → total de registros en LOGS

📲 Integración en Telegram

Se agregó una nueva opción en el menú principal:

Menú principal

1. Crear solicitud  
2. Consultar estado  
3. Mis solicitudes  
4. Reportes   ← NUEVO  
5. Configuración  
9. Volver  
📊 Submenú de Reportes
Reportes

1. Reporte por tipo de solicitud  
9. Volver al menú principal  
🤖 Flujo en n8n

El flujo implementado sigue esta lógica:

Telegram Trigger
      │
     Switch (opciones)
      │
      4 (Reportes)
      │
Mensaje: "Perfecto, estoy generando el reporte..."
      │
Google Sheets (leer SOLICITUDES)
      │
Function (procesar datos)
      │
Google Sheets (leer LOGS)
      │
Function (generar resumen)
      │
Telegram (mostrar reporte)
💬 Mensaje obligatorio del bot

Antes de generar el reporte:

Perfecto, estoy generando el reporte de solicitudes.
📈 Ejemplo de salida en Telegram
📊 Reporte de solicitudes

📌 Resumen general
- Tipo más frecuente: Soporte técnico
- Total solicitudes: 24
- Interacciones con el bot: 60

📂 Detalle por tipo

1) Soporte técnico
   - Total: 12
   - Abiertas: 4
   - En proceso: 5
   - Cerradas: 3

2) Administrativa
   - Total: 8
   - Abiertas: 2
   - En proceso: 3
   - Cerradas: 3
🛠️ Implementación Técnica
🔹 Nodo clave: Function

Se utiliza un nodo Function para:

Recorrer todas las solicitudes

Agrupar por tipo

Contar estados

Determinar el tipo más frecuente

Ejemplo de lógica:

const data = items.map(item => item.json);

const resumen = {};
let total = 0;

data.forEach(s => {
  const tipo = s.tipo || "Sin tipo";
  const estado = (s.estado || "").toLowerCase();

  if (!resumen[tipo]) {
    resumen[tipo] = {
      total: 0,
      abiertas: 0,
      en_proceso: 0,
      cerradas: 0
    };
  }

  resumen[tipo].total++;
  total++;

  if (estado === "abierto") resumen[tipo].abiertas++;
  if (estado === "en proceso") resumen[tipo].en_proceso++;
  if (estado === "cerrado") resumen[tipo].cerradas++;
});

// Tipo más frecuente
let tipoFrecuente = "";
let max = 0;

for (const tipo in resumen) {
  if (resumen[tipo].total > max) {
    max = resumen[tipo].total;
    tipoFrecuente = tipo;
  }
}

return [{
  json: {
    resumen,
    total,
    tipoFrecuente
  }
}];
✅ Requisitos cumplidos

✔ Lectura desde SOLICITUDES
✔ Agrupación por tipo
✔ Conteo por estado
✔ Cálculo de resumen general
✔ Integración en menú principal
✔ Visualización en Telegram
✔ Sin uso de endpoints externos
✔ Uso de datos existentes

📦 Entrega

✔ Nuevo commit en el repositorio

✔ Flujo actualizado en n8n

✔ Evidencias del reporte en Telegram

✔ README actualizado

🚀 Resultado Final

El bot ahora permite:

Consultar información operativa en tiempo real

Analizar uso del sistema

Tomar decisiones basadas en datos

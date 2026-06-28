1. ¿Por qué es mala práctica usar SELECT * en producción?
Usar SELECT * (seleccionar todas las columnas) es una de las tentaciones más comunes al escribir SQL, pero en entornos de producción es una mala práctica crítica.
Dos de las razones más importantes son:

A. Rendimiento (Consumo innecesario de recursos)Cuando usás *, obligás a la base de datos a leer y transmitir absolutamente todos los datos de todas las columnas, incluso las que no necesitás.
El problema: Si tenés una tabla con millones de filas y columnas pesadas (como textos largos, blobs o imágenes), vas a saturar el ancho de banda de la red, aumentar el uso de memoria RAM y ralentizar la consulta. Además, impide que la base de datos aproveche los índices de manera eficiente.

B. Mantenibilidad y Robustez del CódigoEl código de producción debe ser predecible. SELECT * hace que tu aplicación dependa ciegamente de la estructura actual de la tabla.
El problema: Si mañana un administrador de la base de datos (DBA) cambia el orden de las columnas, elimina una columna que no usabas, o añade una nueva columna muy pesada, tu aplicación se puede romper. Al especificar las columnas exactas, tu código se vuelve inmune a los cambios estructurales de las columnas que no te interesan.

Ejemplo concreto: Imaginá una tabla llamada usuarios que tiene 20 columnas, incluyendo campos pesados como foto_perfil (en base64) y biografia.Mal (Producción): SELECT * FROM usuarios WHERE id = 10; (Trae megabytes de información de la foto y la biografía cuando solo querías validar el login).
Bien (Producción): SELECT email, password_hash FROM usuarios WHERE id = 10; (Solo viajan unos pocos bytes por la red, haciendo la consulta ultra rápida).

2. Importancia de los alias para un Stakeholder no técnicoPara un programador o un analista de datos, nombres como total_amount, usr_id o dt_creat son moneda corriente. Sin embargo, para un stakeholder no técnico (un gerente, un director de finanzas o un cliente), ver un reporte con esos encabezados genera fricción, confusión y desconfianza.Los alias (AS) son fundamentales porque actúan como un "traductor" automático. Transforman el lenguaje técnico y rígido de la base de datos en el lenguaje del negocio, permitiendo que los tomadores de decisiones entiendan un reporte al instante sin tener que andar adivinando qué significa cada columna.

Ejemplo concreto de transformación: Imaginá que exportás un reporte financiero directo de la base de datos para presentárselo al Director de Finanzas (CFO). Si no usás alias, la consulta se vería así:SQLSELECT id_factura, total_amount, fecha_reg FROM ventas;
Resultado en el reporte: id_facturatotal_amountfecha_reg98452150000.502026-06-28Al CFO le va a incomodar ver términos en inglés mezclados con español, guiones bajos o abreviaciones técnicas.Aplicando alias, transformamos la consulta para el negocio:
SQLSELECT 
    id_factura AS [Número de Factura], 
    total_amount AS [Monto Total Facturado ($)], 
    fecha_reg AS [Fecha de Registro] 
FROM ventas;

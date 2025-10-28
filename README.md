Este es un proyecto real de Analytics Engineering desarrollado plenamente con DAX para Power BI.

A partir de dos reportes de plataformas de pago diferentes, se los unificó en una única tabla o entidad coherente.

En primer lugar, a partir de un estudio de data profiling, se analizaron en profundidad los datos disponibles para cada reporte.

Se identificaron las columnas en común, sus valores equivalentes y diferentes. Las columnas innecesarias o que no tenían un campo correlativo fueron descartadas.

Se analizaron las particularidades e inconvenientes de los reportes: datos faltantes, incongruencias, incompatibilidad de información, duplicación de registros.

Se limpiaron y estandarizaron los valores por medio de la creación de nuevas variables o campos.

A partir de los datos disponibles se definieron nuevas columnas pertinentes para el análisis.

Se realizaron diferentes pruebas con visualizaciones para evaluar que las modificaciones funcionaran correctamente.

La información corresponde a dos plataformas de pago, que llamaremos PayPlus (PP) y Mundi Wallet (MW).

A continuación se detallan las definiciones principales del código aportado:


Las siguientes columnas se compilaron en un mismo campo sin realizar modificaciones por tratarse de información comparable y equivalente entre sí (variables numéricas, ID y tipo fecha):

<pre> - Date            (PP) <-> (MW)  Fecha_Trx            = Fecha de la transacción
 - Terminal        (PP) <-> (MW)  ID_Tienda            = Tienda de la transacción
 - Amount          (PP) <-> (MW)  Monto_Trx            = Importe abonado en la transacción </pre>

Se encontró que las siguientes columnas, si bien no siempre referían a la misma información, por medio de modificaciones podían utilizarse para crear nuevas variables equivalentes:

<pre>- Status         (PP) <-> (MW)  Tipo_Trx                        = Estado de trx (aprobada, anulada, etc.)
- MobileApp      (PP) <-> (MW)  Billetera_Interoperable_Nombre  = Aplicación virtual utilizada
- Brand          (PP) <-> (MW)  Metodo_Pago                     = Bandera de la tarjeta de crédito
- Bank           (PP) <-> (MW)  Banco_Interoperable_Nombre      = Banco/entidad financiera origen del pago
- Type           (PP) <-> (MW)  Tipo_Metodo_Pago                = Tipo de pago (débito, crédito, etc.)
- Installments Q (PP) <-> (MW)  #Cuotas                         = Cantidad de cuotas con que se pagó. </pre>

A partir de la información disponible se definieron nuevas columnas:

<pre>- Plataforma = plataforma desde la cual se originó el pago. Admite solo: Mundi Wallet/PayPlus.
- Entidad identificada = si el banco o entidad de pago está identificado o no.
- Tipo de Cuota = si el crédito corre por tasa sistémica, preferencial, si es interno a una plataforma.
- Cuota (c/tipo) = la cantidad de cuotas otorgadas junto con el tipo de crédito que le corresponde. </pre>

A la par, se definieron dos tipos de variables de cuotas:

<pre>- Cuotas_cat = para medir datos categóricos. 
- Cuotas_num = para realizar cálculos numéricos </pre>

Asimismo, se eliminaron registros innecesarios: transacciones rechazadas, desembolsos no correspondiente a pagos, pagos repetidos entre reportes (dada la interoperabilidad de plataformas).
En el caso de los registros duplicados, se resolvió dejar los de PayPlus, ya que su reporte se encuentra más limpio y completo. 

Como resultado final, se creó una única tabla con registros equivalentes y comparables entre sí con los siguientes campos:

<pre>- Fecha                = Fecha de la transacción
- ID Tienda            = ID de la tienda donde se realizó la transacción.
- Status               = Estado de la transacción (aprobada, anulada, reembolsada, etc.) 
- Plataforma           = Plataforma desde la que se realizó el pago (Mundi Wallet, Pay Plus) 
- Aplicación Móvil     = Aplicación desde la que se realizó el pago.
- Bandera              = Bandera de la tarjeta (si no, se define el pago como transferencia)
- Entidad de pago      = Banco o entidad financiera origen del pago.
- Entidad identificada = Si el banco o entidad financiera origen del pago está identificado (Sí, No).
- Tipo de pago         = Tipo de pago de la transacción (Débito, Crédito, Dinero Disponible, etc.)
- Tipo de cuota        = Tipo de crédito (Tasa sistémica, Tasa Preferencial, Crédito Interno, etc.).      
- Cuotas_cat           = Var nominal. Se aclara si no hay datos o si corresponden a otro tipo de pago.
- Cuotas_num           = Var discreta. Solo considera crédito. Si no, asume vacío.
- Cuota (c/tipo)       = Cantidad de cuotas con que se pagó + Tipo de cuota otorgada
- Importe Neto $ARS    = Monto abonado (en $ARS). </pre>





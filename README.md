Este es un proyecto real de Analytics Engineering desarrollado plenamente con DAX para Power BI.

A partir de dos reportes de plataformas de pago diferentes, se los unificó en una única tabla o entidad coherente.

En primer lugar, a partir de un estudio de data profiling, se analizaron en profundidad los datos disponibles para cada reporte.

Se identificaron las columnas en común, sus valores equivalentes y diferentes. Las columnas innecesarias o que no tenían un campo correlativo fueron descartadas.

Se analizaron las particularidades e inconvenientes de los reportes: datos faltantes, incongruencias, incompatibilidad de información, duplicación de registros.

Se limpiaron y estandarizaron los valores por medio de la creación de nuevas variables o campos.

A partir de los datos disponibles se definieron nuevas columnas pertinentes para el análisis.

Se realizaron diferentes pruebas con visualizaciones para evaluar que las modificaciones funcionaran correctamente.

La información corresponde a dos plataformas de pago, que llamaremos PayPlus y Mundi Wallet.

A continuación se detallan las definiciones principales del código aportado:


Las siguientes columnas se compilaron en un mismo campo sin realizar modificaciones por tratarse de información comparable y equivalente entre sí (variables numéricas, ID y tipo fecha):

<pre> - Date            (PayPlus) <-> (Mundi Wallet)  Fecha_Trx                   = Fecha de la transacción
 - Terminal        (PayPlus) <-> (Mundi Wallet)  ID_Tienda                         = Tienda de la transacción
 - Amount          (PayPlus) <-> (Mundi Wallet)  Monto_Trx                         = Importe abonado en la transacción </pre>

Se encontró que las siguientes columnas, si bien no siempre referían a la misma información, por medio de modificaciones podían utilizarse para crear nuevas variables equivalentes:

<pre>- Status          (PayPlus) <-> (Mundi Wallet)  Tipo_Trx                          = Estado de la trx (aprobada, reembolsada, etc.)
- MobileApp       (PayPlus) <-> (Mundi Wallet)  Billetera_Interoperable_Nombre    = Aplicación virtual utilizada
- Brand           (PayPlus) <-> (Mundi Wallet)  Metodo_Pago                       = Bandera de la tarjeta de crédito
- Bank            (PayPlus) <-> (Mundi Wallet)  Banco_Interoperable_Nombre        = Banco o entidad financiera origen del pago
- Type            (PayPlus) <-> (Mundi Wallet)  Tipo_Metodo_Pago                  = Tipo de pago (débito, crédito, etc.)
- Installments Q  (PayPlus) <-> (Mundi Wallet)  #Cuotas                           = Cantidad de cuotas con que se pagó. </pre>

A partir de la información disponible se definieron nuevas columnas:

<pre>- Plataforma = plataforma desde la cual se originó el pago. Admite solo dos valores: Mundi Wallet/PayPlus.
- Entidad identificada = si la entidad de pago está identificada o no (muchos pagos con tarjeta de crédito no indican el banco).
- Tipo de Cuota = si el crédito corre por tasa sistémica, preferencial, si es interno a una plataforma, si no hay datos.
- Cuota (c/tipo) = la cantidad de cuotas otorgadas junto con el tipo de crédito que le corresponde </pre>

A la par, se definieron dos tipos de variables de cuotas:

<pre>- Cuotas_cat = para medir datos categóricos
- Cuotas_num = para realizar cálculos numéricos </pre>

Asimismo, se eliminaron registros innecesarios: transacciones rechazadas, desembolsos no correspondiente a pagos, pagos repetidos entre reportes (dada la interoperabilidad de plataformas).
En el caso de los registros duplicados, se resolvió dejar los de PayPlus, ya que su reporte se encuentra más limpio y completo. 

Como resultado final, se creó una única tabla con registros equivalentes y comparables entre sí con los siguientes campos:

<pre>- Fecha                 = Fecha de la transacción
- ID Tienda             = ID de la tienda donde se realizó la transacción.
- Status                = Estado de la transacción (aprobada, anulada, reembolsada, etc.) 
- Plataforma            = Plataforma desde la que se realizó el pago (Mundi Wallet, Pay Plus) 
- Aplicación Móvil      = Aplicación desde la que se realizó el pago.
- Bandera               = Bandera correspondiente a la tarjeta (si se abonó de otra forma se lo define como transferencia)
- Entidad de pago       = Banco o entidad financiera origen del pago.
- Entidad identificada  = Si el banco o entidad financiera origen del pago está identificado (Sí, No).
- Tipo de pago          = Tipo de pago de la transacción (Débito, Crédito, Dinero Disponible, etc.)
- Tipo de cuota         = Tipo de crédito (Tasa sistémica, Tasa Preferencial, Crédito Interno, Crédito #N/D, No crédito).      
- Cuotas_cat            = Cuotas. Si no hay datos define "Crédito #N/D", si corresponde a otros tipos de pago se lo aclara.
- Cuotas_num            = Cuotas. Solo considera pago c/crédito. Asume vacío para otros tipos de pago o si no hay información.
- Cuota (c/tipo)        = Cantidad de cuotas con que se pagó + Tipo de cuota otorgada
- Importe Neto $ARS"    = Monto abonado (en $ARS). </pre>





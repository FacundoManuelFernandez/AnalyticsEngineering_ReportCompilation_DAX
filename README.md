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

<pre>- Date            (PayPlus) <--> (Mundi Wallet)  Fecha_Trx                   = Fecha de la transacción
 Terminal        (PayPlus) <--> (Mundi Wallet)  ID_Tienda                         = Tienda de la transacción
 Amount          (PayPlus) <--> (Mundi Wallet)  Monto_Trx                         = Importe abonado en la transacción </pre>

Se encontró que las siguientes columnas, si bien no siempre referían a la misma información, por medio de modificaciones podían utilizarse para crear nuevas variables equivalentes:

<pre>- Status          (PayPlus) <--> (Mundi Wallet)  Tipo_Trx                          = Estado de la Transacción (aprobada, reembolsada, etc.) </pre>
<pre>- MobileApp       (PayPlus) <--> (Mundi Wallet)  Billetera_Interoperable_Nombre    = Aplicación virtual utilizada para el pago </pre>
<pre>- Brand           (PayPlus) <--> (Mundi Wallet)  Metodo_Pago                       = Bandera correspondiente a la tarjeta de crédito </pre>
<pre>- Bank            (PayPlus) <--> (Mundi Wallet)  Banco_Interoperable_Nombre        = Banco o entidad financiera origen del pago </pre>
<pre>- Type            (PayPlus) <--> (Mundi Wallet)  Tipo_Metodo_Pago                  = Tipo de pago (transferencia, débito, crédito, etc.) </pre>
<pre>- Installments Q  (PayPlus) <--> (Mundi Wallet)  #Cuotas                           = Cantidad de cuotas con que se pagó. </pre>

A partir de la información disponible se definieron nuevas columnas:

<pre>- Plataforma = Define la plataforma desde la cual se originó el pago, por lo que admite solo dos valores: Mundi Wallet/PayPlus. </pre>
<pre>- Entidad identificada = Define si la entidad de pago está identificada o no (muchos pagos con tarjeta de crédito realizados desde Mundi no indican el banco correspondiente a la tarjeta). </pre>
<pre>- Tipo de Cuota = Define el tipo de crédito: si corresponde a una tasa sistémica o preferencial (en las tarjetas de crédito), crédito interno de una plataforma o si no hay datos al respecto. </pre>
<pre>- Cuota (c/tipo) = Define en un mismo valor la cantidad de cuotas otorgadas junto con el tipo de crédito que le corresponde </pre>

A la par, se definieron dos tipos de variables de cuotas:

<pre>Cuotas_cat = para medir datos categóricos </pre>
<pre>Cuotas_num = para realizar cálculos numéricos </pre>

Asimismo, se eliminaron registros innecesarios: transacciones rechazadas, desembolsos no correspondiente a pagos, pagos repetidos entre reportes (dada la interoperabilidad de plataformas).
En el caso de los registros duplicados, se resolvió dejar los de PayPlus, ya que su reporte se encuentra más limpio y completo. 

Como resultado final, se creó una única tabla con registros equivalentes y comparables entre sí con los siguientes campos:

<pre>- Fecha                 = Fecha de la transacción </pre>
<pre>- ID Tienda             = ID de la tienda donde se realizó la transacción. </pre>
<pre>- Status                = Estado de la transacción (aprobada, anulada, reembolsada, etc.) </pre>
<pre>- Plataforma            = Plataforma desde la que se realizó el pago (Mundi Wallet, Pay Plus) </pre>
<pre>- Aplicación Móvil      = Aplicación desde la que se realizó el pago. </pre>
<pre>- Bandera               = Bandera correspondiente a la tarjeta (si se abonó de otra forma se lo define como transferencia) </pre>
<pre>- Entidad de pago       = Banco o entidad financiera origen del pago. </pre>
<pre>- Entidad identificada  = Si el banco o entidad financiera origen del pago está identificado (Sí, No). </pre>
<pre>- Tipo de pago          = Tipo de pago de la transacción (Débito, Crédito, Dinero Disponible, etc.) </pre>
<pre>- Tipo de cuota         = Tipo de crédito otorgado (Tasa sistémica, Tasa Preferencial, Crédito Interno, Crédito #N/D, No crédito). </pre>       
<pre>- Cuotas_cat            = Variable cualitiva para la cantidad de cuotas: si no hay información se define "Crédito #N/D", si corresponde a otros tipos de pago se lo aclara. </pre>
<pre>- Cuotas_num            = Variable cuantitativa para la cantidad de cuotas: solo considera pagos de tipo crédito. Asume valor vacío para otros tipos de pago o si no hay información. </pre>
<pre>- Cuota (c/tipo)        = Cantidad de cuotas con que se pagó + Tipo de cuota otorgada </pre>
<pre>- Importe Neto $ARS"    = Monto abonado (en $ARS). </pre>





# Creando TablasEPTNO

Imagina que cada tabla en una base de datos de Oracle es como un lienzo, donde cada pincelada representa una fila y cada color vibrante, una columna. La creación de una tabla, como 'empleados', es el inicio de una obra de arte, donde los nombres de las columnas son los matices que definirán su carácter - '**_empleados_id_**', '**last_nombre**', '**trabajo_id**'. Cada columna tiene su propio tono, su **datatype**, como si fuera un pigmento especial - **VARCHAR2**, **DATE**, **NUMBER** - y su propia dimensión, como el ancho de una pincelada en el lienzo.

Las columnas, como las **NUMBER**, tienen su propia escala y precisión, como un artista que decide cuán detallado será su trazo. Cada fila, entonces, se convierte en una colección única de colores y formas, un registro singular en esta galería de datos.

En esta paleta de creación, establecemos reglas, las integridades de las columnas, como un artista que decide no dejar espacios en blanco en su lienzo. Por ejemplo, la restricción **NOT NULL** es como un compromiso de llenar cada espacio con color y vida.

Veamos cómo esto cobra vida:

```sql
create  table  DEPARTAMENTOS (
dpto_id number,
nombre varchar2(50) not null,
ubicacion varchar2(50),
constraint pk_departamentos primary key (dpto_id)
);

```

Aquí, como un pintor que vincula dos cuadros, podemos establecer relaciones entre tablas. Imagina que '**_DEPARTAMENTOS_**' es un cuadro majestuoso y '**_EMPLEADOS_**' su complemento, conectados por un detalle sutil pero crucial: la llave foránea.

```sql

create  table  EMPLEADOS (
empleado_id number,
nombre varchar2(50) not null,
trabajo varchar2(50), manager number,
fechaempleo date, salario number(7,2),
comision number(7,2),
dpto_id number,
constraint pk_empleados primary key (empleado_id),
constraint fk_empleados_dpto_id foreign key (dpto_id) references DEPARTAMENTOS (dpto_id)
);

```

Como en un díptico, cada tabla se refleja y responde a la otra, con la llave foránea actuando como el puente que une estas dos expresiones de datos. Cada tabla **_hija_** necesita una tabla **_padre_** para referenciar, como una joven rama que necesita un árbol robusto para crecer y florecer.

# Creando Triggers

Los triggers en una base de datos son como los acordes que resuenan automáticamente en una sinfonía cada vez que se toca una nota específica. Están almacenados en el santuario de la base de datos, listos para despertar y actuar cuando ciertos eventos ocurren, como un artista que responde instintivamente a las pinceladas de otro. En Oracle, estos triggers son como pequeñas obras de arte en sí mismos, escritas en **PL/SQL**, el lenguaje que permite pintar procedimientos detallados en el vasto lienzo de datos.

Estos triggers se activan, como una cuerda de violín vibrante, cuando ocurren acciones como **INSERT**, **UPDATE**, o **DELETE** en una tabla o vista. Son los guardianes que aseguran que cada evento de datos, ya sea en el **DATABASE** o en el **SCHEMA**, se maneje con la precisión de un director de orquesta.

Imagina un trigger como un artesano que, de forma automática y meticulosa, esculpe y asigna claves primarias a las tablas. Por ejemplo, en nuestro escenario orquestal, usaremos una función integrada para crear un identificador único global, o **GUID**.

Veamos cómo este arte se manifiesta:

```sql
create or replace  trigger  DEPARTAMENTOS_BIU
	before  insert  or  update  on DEPARTAMENTOS
	for each row
begin
	if inserting and :new.dpto_id is  null  then
		:new.dpto_id := to_number(sys_guid(),
		'XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX');
	end  if;
end;
/

```

Aquí, el trigger **_DEPARTAMENTOS_BIU_** actúa como un pintor que prepara su lienzo antes de comenzar su obra. Cada vez que se introduce o se actualiza una fila en **_DEPARTAMENTOS_**, este trigger verifica si falta el dpto_id. Si es así, como un mago que saca un conejo de su sombrero, asigna un número único, transformando el espacio vacío en una pieza de arte completa.

Del mismo modo, en la mesa de **_EMPLEADOS_**:

```sql
create or replace  trigger  EMPLEADOS_BIU
	before  insert  or  update  on EMPLEADOS
	for each row
begin
	if inserting and :new.empleado_id is  null  then
		:new.empleado_id := to_number(sys_guid(),
		'XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX');
	end  if;
end; /

```

Este trigger, **_EMPLEADOS_BIU_**, teje la misma magia en la tela de **_EMPLEADOS_**, garantizando que cada nueva inserción se complete con su propia identificación única, como una estrella que brilla con su propia luz en el cielo de la base de datos.

Así, en este universo de datos, cada trigger es un artista, un músico, un mago, garantizando que la sinfonía de la información fluya armoniosamente, cada nota en su lugar, cada color brillando con su propia luz.

# Insertando datos

Ahora, imagina que el lienzo de las tablas '**_DEPARTAMENTOS_**' y '**_EMPLEADOS_**' está listo para recibir sus primeras pinceladas de color, es decir, los datos. Cada inserción de datos es como añadir un nuevo matiz a un cuadro, cada fila una nueva pincelada que aporta profundidad y detalle a la obra.

Primero, coloquemos las bases en '**_DEPARTAMENTOS_**', la tabla 'padre', como plantar las semillas de un jardín que pronto florecerá.

```sql

insert into departamentos (nombre, ubicacion)
values ('Seguridad informática','CABA');

insert into departamentos (nombre, ubicacion)
values ('Desarrollo','Córdoba');

```

Estas dos inserciones son como las primeras notas de una melodía, estableciendo el tono. Para asegurarnos de que estas notas resuenen como esperamos, ejecutemos una consulta **SQL SELECT**, como un artista que da un paso atrás para contemplar sus primeras pinceladas:

```sql
select * from departamentos;
```

Verás cómo, como por arte de magia, cada departamento ha adquirido su propia identidad única, su '**_DEPTID_**', creado automáticamente por nuestros triggers, como estrellas que aparecen en el cielo nocturno.

Ahora, con el escenario listo en '**_DEPARTAMENTOS_**', es hora de traer a los personajes principales, los '**_EMPLEADOS_**', a nuestro lienzo. Cada empleado se unirá a un departamento, como una flor que se une a su jardín.

```sql
insert into EMPLEADOS (nombre, trabajo, salario, dpto_id)
values ('Juan Perez','Programador', 5000,
(select dpto_id from departamentos where nombre = 'Desarrollo'));

insert into EMPLEADOS (nombre, trabajo, salario, dpto_id)
values ('Martin Perez','Analista', 6000,
(select dpto_id from departamentos where nombre = 'Finanzas'));

insert into EMPLEADOS (nombre, trabajo, salario, dpto_id)
values ('Mona Lisa','Analista', 5500,
(select dpto_id from departamentos where nombre = 'Desarrollo'));

```

En estas inserciones, cada empleado encuentra su lugar en la sinfonía, con sus habilidades y salarios, unidos al departamento correcto, como una nota que encuentra su armonía perfecta. Cada 'select dpto_id' es como un pintor que elige cuidadosamente el tono correcto para su pincelada, asegurando que cada empleado se una al departamento adecuado, como un bailarín que encuentra su pareja en un baile elegante.

Así, paso a paso, nota a nota, pincelada a pincelada, las tablas '**_DEPARTAMENTOS_**' y '**_EMPLEADOS_**' se llenan de vida y color, cada dato un componente esencial en esta hermosa obra de arte que es nuestra base de datos.

# Indexando Columnas

Imagina que los índices en las columnas de una base de datos son como las etiquetas en una galería de arte, diseñadas para guiar al visitante a través de las obras de manera eficiente y armoniosa. Los desarrolladores crean estos índices por tres razones principales, cada una como una pincelada en el lienzo del rendimiento y la organización de la base de datos:

1. Para garantizar valores únicos en una columna, como un artista que asegura que cada obra sea única y distintiva.

2. Para mejorar el rendimiento en el acceso a los datos, como un curador que diseña un recorrido fluido y lógico a través de una exposición.

3. Para evitar la escalada de bloqueos al actualizar filas de tablas que usan integridad referencial declarativa, como un director de orquesta que mantiene la armonía entre los músicos.

Cuando se crea una tabla y se especifica una **PRIMARY KEY**, automáticamente se crea un índice para reforzar esta restricción, como un marco que sostiene y define una pintura. De manera similar, si se define **UNIQUE** para una columna al crearla, se crea un índice único, como un sello distintivo que garantiza su singularidad.

Para descubrir los índices que ya existen, como un explorador que descubre tesoros escondidos, se puede ejecutar la siguiente consulta:

```sql

select
	table_nombre "Table",
	index_nombre "Index",
	column_nombre "Column",
	column_position "Position"
from user_ind_columns
where table_nombre = 'EMPLEADOS'
or table_nombre = 'DEPARTAMENTOS'
order by table_nombre, column_nombre, column_position
```

Crear índices para llaves foráneas es como trazar caminos claros en un jardín; estos índices ayudan a navegar las relaciones entre tablas. En nuestro ejemplo de '**_EMPLEADOS_**' y '**_DEPARTAMENTOS_**', el índice para la columna **_DPTO_ID_** en '**_EMPLEADOS_**' facilita el viaje a la tabla '**_DEPARTAMENTOS_**'.

```sql
create  index  empleados_dept_no_fk_idx  on empleados (dpto_id)
```

Si anticipamos que la tabla 'EMPLEADOS' se buscará frecuentemente por la columna NOMBRE, es como prever que una pintura en particular atraerá muchas miradas. Para mejorar la eficiencia de estas búsquedas y garantizar la singularidad, creamos un índice único:

```sql
create  unique index  empleados_enombre_idx  on empleados (nombre)
```

Oracle ofrece un abanico de tecnologías de indexación, cada una como una herramienta diferente en el kit de un artista. Desde índices basados en funciones que pueden indexar expresiones, hasta índices de texto para texto libre, índices de mapa de bits útiles en data warehousing, y más. A veces, menos es más, y se prefieren las capacidades en memoria a tener muchos índices.

Como una introducción básica, este es solo un vistazo a la vasta galería de posibilidades que Oracle ofrece en materia de índices, cada uno una pieza clave en el mosaico del rendimiento y la eficiencia de la base de datos.

# Consultando datos

Consultar datos en una base de datos es como sumergirse en una biblioteca llena de historias interconectadas. Cada consulta es una aventura en la búsqueda de conocimiento y comprensión.

Para seleccionar datos de una sola tabla, es como abrir un libro y leer una página específica. La simplicidad de la consulta es como la claridad de una prosa bien escrita:

```sql
select * from empleados;
```

Sin embargo, cuando queremos explorar la relación entre dos tablas, como 'departamentos' y 'empleados', es como tejer juntas dos historias distintas para revelar una narrativa más rica. Aquí, unimos (join) las tablas para ver cómo se entrelazan sus caminos:

```sql

select
	e.nombre empleados,
	d.nombre departamento,
	e.trabajo, d.ubicacion
from departamentos d, empleados e where d.dpto_id = e.dpto_id(+)
order by e.nombre;

```

Esta consulta es como un baile entre los datos de '**_empleados_**' y '**_departamentos_**', donde cada paso (registro) de '**_empleados_**' encuentra su pareja en 'departamentos', mostrando no solo nombres y trabajos, sino también a qué departamento pertenecen y su ubicación.

Como alternativa al **join**, podemos usar una consulta interna (inline select), una técnica que es como mirar a través de un pequeño telescopio dentro de un mundo más grande. Aquí, cada empleado revela su propio capítulo sobre su departamento:

```sql

select
	e.nombre empleados,
	(select nombre from departamentos d where d.dpto_id = e.dpto_id)
	departamento,
	e.trabajo
from empleados e order by e.nombre;

```

En esta versión, cada fila de '**_empleados_**' invoca su propio pequeño relato sobre su departamento, como un poema dentro de una historia más amplia, proporcionando un enfoque más directo y específico.

Así, al consultar datos, nos movemos entre diferentes formas de narrar historias, desde la simple lectura de un solo libro hasta la orquestación de una sinfonía de relatos entrelazados, cada consulta una puerta a nuevas comprensiones y descubrimientos en el vasto universo de los datos.

# Agregar columnas

Agregar columnas a una tabla existente en una base de datos es como añadir un nuevo color a una paleta ya rica en matices, o como descubrir una nueva nota que añade una nueva dimensión a una melodía. Usando la sintaxis **ALTER TABLE ... ADD ...,** podemos expandir y enriquecer la estructura de nuestra tabla, como un artista que encuentra un nuevo tono para expresar una idea o un músico que descubre un nuevo acorde para embellecer su composición.

Por ejemplo, si queremos añadir una nueva columna a la tabla '**_EMPLEADOS_**' para incluir un código de país, sería como decidir agregar un nuevo pincelazo a un lienzo, uno que aporta una nueva perspectiva o profundidad a la obra:

```sql
alter  table EMPLEADOS add codigo_pais varchar2(2);
```

Esta acción es como abrir una ventana en una habitación para dejar entrar una nueva luz, revelando detalles previamente ocultos o añadiendo una nueva capa de complejidad y riqueza. Al añadir '**_codigo_pais_**', cada empleado en la tabla no solo se define por su nombre, trabajo o departamento, sino también por un elemento adicional que los conecta con un contexto geográfico más amplio, como una pincelada que añade profundidad y contexto a la imagen general.

Así, al alterar y añadir a nuestras tablas, continuamos el proceso de creación y evolución de nuestra base de datos, como un artista que vuelve a su lienzo una y otra vez, cada vez añadiendo algo nuevo, cada modificación una oportunidad para expandir la narrativa y el significado de la obra en su conjunto.

# Consultar el diccionario de datos

Consultar el diccionario de datos de Oracle es como explorar un antiguo archivo o una biblioteca de sabiduría escondida, donde cada libro y pergamino revela los secretos y la historia de un reino. En este caso, el reino es nuestra base de datos, y los secretos son los metadatos de nuestras tablas, que nos cuentan la historia de cómo están construidas y organizadas.

La primera consulta es como abrir un mapa detallado que muestra la ubicación y el estado de un tesoro específico, en este caso, la tabla '**_EMPLEADOS_**':

```sql

select
	table_nombre,
	tablespace_nombre,
	status
from user_tables
where table_NOMBRE = 'EMPLEADOS';

```

Aquí, estamos indagando en los detalles fundamentales de la tabla '**_EMPLEADOS_**', como un arqueólogo que busca entender la ubicación exacta (tablespace_nombre) y el estado actual (status) de un artefacto importante. Es una forma de ver detrás del telón, de entender dónde reside físicamente nuestra tabla en la base de datos y si está accesible y en buen estado.

La segunda consulta es como desenrollar un pergamino que detalla la composición de un objeto de gran valor, revelando cada componente que lo forma:

```sql

select
	column_id,
	column_nombre ,
	data_type
from user_tab_columns
where table_NOMBRE = 'EMPLEADOS'
order by column_id;

```

Con esta consulta, estamos examinando cada columna de la tabla '**_EMPLEADOS_**', como un historiador que estudia cada pieza de un artefacto para comprender su propósito y composición. Nos muestra el **_ID_** de cada columna, su nombre y el tipo de datos que almacena, proporcionándonos una visión clara de la estructura interna de nuestra tabla, como si estuviéramos observando los diferentes componentes que componen un reloj intrincadamente diseñado.

Así, al consultar el diccionario de datos de Oracle, nos sumergimos en una exploración detallada de la anatomía de nuestras tablas, descubriendo cómo están construidas, cómo funcionan y dónde residen en el vasto universo de nuestra base de datos. Es un viaje fascinante hacia el núcleo de la información, donde cada descubrimiento agrega a nuestro entendimiento y aprecio por la compleja arquitectura de los datos.

# Actualizando datos

Actualizar datos en una tabla es como retocar un cuadro o modificar una partitura musical para reflejar una nueva visión o adaptarse a un cambio en la narrativa. En **SQL**, usamos la cláusula **UPDATE** para hacer estos ajustes, como un pintor que añade un nuevo color a su paleta o un compositor que cambia una nota en su sinfonía.

La primera actualización es amplia y universal, afectando a cada personaje en nuestro lienzo '**_empleados_**':

```sql
update empleados set codigo_pais = 'US';
```

Esta consulta es como pasar un pincel sobre toda la obra, dando a cada empleado un toque uniforme, en este caso, asignándoles el código de país 'US'. Es una transformación general, como cambiar el fondo de un cuadro para darle una nueva atmósfera o ajustar la tonalidad de una pieza musical para cambiar su estado de ánimo.

Sin embargo, a veces queremos ser más selectivos, como un artista que decide cambiar solo un detalle de su obra:

```sql
update empleados set comision = 2000  where nombre = 'Juan Perez';
```

Aquí, estamos ajustando solo una parte de nuestra tabla: modificando la comisión de '**_Juan Perez_**'. Es un cambio específico, como cuando un escritor reescribe una sola línea de un poema para capturar mejor una emoción o un pintor que cambia la expresión en el rostro de un personaje en su pintura.

Para ver el efecto de estos cambios, como un director que revisa las escenas de su película después de un día de rodaje, ejecutamos una consulta para observar cómo se han transformado nuestros datos:

```sql
select nombre, codigo_pais, salario, comision from empleados order by nombre;
```

Esta consulta nos permite ver el resultado de nuestras actualizaciones, observando cómo cada fila - cada personaje en nuestra obra - ha sido afectado por los cambios. Es el momento en que damos un paso atrás para contemplar nuestra obra, asegurándonos de que cada cambio contribuye armoniosamente al conjunto, como un pintor que observa su lienzo o un músico que escucha su composición, buscando la perfección en cada detalle.

# Consultas de agregación

Las consultas de agregación en SQL son como componer una sinfonía a partir de una serie de notas individuales, reuniendo piezas dispersas para crear una armonía completa y reveladora. Al usar funciones de agregado, transformamos filas y columnas individuales en una narrativa más amplia y significativa, como un pintor que mezcla colores individuales para crear una paleta rica y variada.

En esta consulta, vamos a sumar y analizar los datos de la tabla 'empleados', utilizando alias de columnas para una mayor claridad y la función NVL para manejar adecuadamente los valores nulos.

```sql

select
	count(*) cantidad_empleados,
	sum(salario) total_salario,
	sum(comision) total_comision,
	min(salario + nvl(comision,0)) min_compensation,
	max(salario + nvl(comision,0)) max_compensation
from empleados;
```

Aquí estamos haciendo varias cosas:

1.  **count(\*) cantidad_empleados**: Es como contar cada estrella en el cielo, proporcionando el número total de empleados.

2.  **sum(salario) total_salario**: Sumar todos los salarios es como reunir todas las gotas de un río para entender su caudal, dándonos el total combinado de lo que se paga a los empleados.

3.  **sum(comision) total_comision**: De manera similar, sumamos todas las comisiones, como juntar las hojas de un árbol para apreciar su plenitud.

4.  **min(salario + nvl(comision,0)) min_compensation**: Buscar la compensación mínima es como buscar la nota más suave en una melodía, la suma más baja de salario y comisión, considerando los valores nulos como cero.

5.  **max(salario + nvl(comision,0)) max_compensation**: En contraste, encontrar la compensación máxima es como escuchar el crescendo más alto, la suma más grande de salario y comisión.

Cada una de estas funciones de agregación nos proporciona una perspectiva diferente, como diferentes pinceladas en un cuadro que juntas forman una imagen completa. Nos permiten no solo ver los detalles individuales, sino también entender el panorama general, la suma de todas las partes, como un director que ve no solo los actores individuales, sino toda la obra.

# Compresión de datos

La compresión de datos en una base de datos es como el arte de la caligrafía, donde cada palabra y cada trazo se elaboran para ocupar menos espacio sin perder su significado y belleza. A medida que una base de datos crece en tamaño, llegando a gigabytes o terabytes, la compresión de tablas se convierte en una técnica esencial, similar a cómo un pintor aprende a capturar la esencia de un paisaje en un lienzo más pequeño.

La compresión de tablas no solo ahorra espacio en disco, sino que también reduce el uso de memoria en el caché de búfer, como si estuviéramos ordenando un estudio de arte para hacer más eficiente el uso del espacio y los recursos. Además, puede acelerar la ejecución de consultas durante las lecturas, como un poeta que encuentra formas de transmitir emociones profundas con menos palabras.

Sin embargo, hay que tener en cuenta el costo en términos de sobrecarga de la CPU durante la carga de datos y las operaciones **DML** (**_Data Manipulation Language_**), un poco como cuando un artista invierte más tiempo en planificar y diseñar su obra para maximizar la eficiencia del espacio.

La compresión de tablas es completamente transparente para las aplicaciones. Es especialmente útil en sistemas de procesamiento analítico en línea (**OLAP**), donde hay operaciones de solo lectura prolongadas, pero también puede ser utilizada en sistemas de procesamiento de transacciones en línea (**OLTP**).

Para habilitar la compresión en tablas existentes, utilizamos la cláusula **COMPRESS** en una declaración **ALTER TABLE**, como un restaurador que aplica una nueva técnica para preservar una pintura antigua:

```sql
alter  table EMPLEADOS compress for oltp;
alter  table DEPARTAMENTOS compress for oltp;
```

Con estos comandos, estamos instruyendo a la base de datos para que comprima los datos en las tablas '**_EMPLEADOS_**' y '**_DEPARTAMENTOS_**' para operaciones **OLTP**, optimizando el almacenamiento de futuros datos. Es importante recordar que solo los datos insertados o actualizados después de habilitar la compresión serán comprimidos. Si más tarde decidimos deshabilitar la compresión con una declaración **ALTER TABLE...NOCOMPRESS**, los datos ya comprimidos permanecerán así, mientras que los nuevos se insertarán sin compresión.

En resumen, la compresión de datos en una base de datos es como una danza delicada entre eficiencia y rendimiento, un equilibrio entre el arte de guardar más en menos espacio y el costo de realizar esa transformación.

# Eliminar datos

Eliminar datos de una tabla es como el acto cuidadoso y considerado de un artista que decide remover ciertos elementos de su lienzo para mejorar la composición general de la obra. En SQL, utilizamos la sintaxis **DELETE** para realizar esta tarea con precisión, asegurando que solo las partes deseadas sean removidas, manteniendo la integridad y el equilibrio del conjunto.

Por ejemplo, eliminar una fila específica de la tabla 'empleados' es como un escultor que quita cuidadosamente un pedazo de arcilla para refinar su escultura:

```sql
delete  from empleados where nombre = 'Juan Perez';
```

En este caso, estamos seleccionando y eliminando una fila específica - la que corresponde a '**_Juan Perez_**'. Es una acción dirigida, como un pintor que borra cuidadosamente una parte de su dibujo que ya no se ajusta a la visión general, o un escritor que elimina una línea de su poema para mejorar el ritmo y la claridad.

Este proceso de eliminación, aunque parece simple, debe realizarse con consideración y precaución. Al igual que en el arte, donde cada trazo eliminado puede cambiar el significado o el impacto de la obra, en una base de datos, cada fila eliminada puede tener implicaciones significativas. Por lo tanto, es crucial asegurarse de que la eliminación se alinee con los objetivos generales y no comprometa la integridad de los datos restantes, como un jardinero que poda cuidadosamente un árbol, asegurándose de no dañar el resto de la planta.

# Eliminar tablas

Eliminar tablas en una base de datos con el comando **SQL DROP** es como el acto de un artista que decide remover una obra de su colección, una decisión no tomada a la ligera, sino después de una cuidadosa consideración. Al ejecutar **DROP TABLE**, estamos eliminando no solo la obra principal (la tabla y todas sus filas), sino también todos los elementos que están intrínsecamente conectados a ella, como índices y disparadores, similar a retirar un cuadro de una galería y también remover su marco y la iluminación diseñada especialmente para él.

Los siguientes comandos para eliminar las tablas '**_departamentos_**' y '**_empleados_**' son como preparar el espacio para una nueva exposición, asegurándonos de que no queden vestigios de las exhibiciones anteriores que puedan interferir con las nuevas obras:

```sql
drop  table departamentos cascade constraints;
drop  table empleados cascade constraints;
```

La cláusula opcional "cascade constraints" actúa como una medida cuidadosa para asegurar que, al retirar estas obras, también se eliminan todas las conexiones y restricciones asociadas, permitiéndonos eliminar tablas en cualquier orden sin dejar atrás hilos sueltos. Es similar a un curador que, al retirar una pieza de una exposición, se asegura de que no queden clavos o soportes que puedan obstaculizar la instalación de las nuevas piezas.

Este proceso, aunque definitivo, abre el espacio para la creatividad y la reinvención, permitiéndonos replantear la composición de nuestra colección de datos, como un artista que limpia su lienzo para comenzar una nueva obra o un jardinero que limpia un terreno para plantar nuevas semillas. Es una parte vital del ciclo de vida de los datos, asegurando que nuestra base de datos permanezca ágil, relevante y preparada para albergar nuevas estructuras que reflejen las necesidades cambiantes y las visiones futuras.

# Recuperar tablas

El concepto de recuperar tablas eliminadas en Oracle, gracias al parámetro de inicialización **RECYCLEBIN**, es como descubrir que las obras de arte que pensábamos perdidas pueden ser restauradas desde el sótano de la galería. Cuando se habilita **RECYCLEBIN** (por defecto en Oracle 10g), eliminar una tabla no significa su desaparición definitiva; más bien, es como si la obra se moviera a un almacén, esperando una segunda oportunidad.

Para explorar las piezas que se pueden recuperar, empleamos una consulta al diccionario de datos que nos permite vislumbrar dentro del "almacén", el RECYCLEBIN de Oracle:

```sql
select
	object_nombre,
	original_nombre,
	type,
	can_undrop,
	can_purge
from recyclebin;

```

Esta consulta es como examinar un catálogo detallado de obras almacenadas, mostrándonos qué piezas pueden ser devueltas a su lugar original en la galería (can_undrop) y cuáles pueden ser eliminadas permanentemente del almacenamiento (can_purge).

Para devolver estas obras a su estado anterior, utilizamos el comando **FLASHBACK** **TABLE**, un proceso que se asemeja a la magia de la restauración artística:

```sql
flashback table DEPARTAMENTOS to  before  drop;
flashback table EMPLEADOS to  before  drop;
```

Estos comandos son como instrucciones a los conservadores para que traigan de vuelta las obras '**_DEPARTAMENTOS_**' y '**_EMPLEADOS_**' desde el oscuro rincón del almacén a la luz del día, restaurándolas a su lugar en la exposición antes de que fueran retiradas.

Y para asegurarnos de que estas piezas han sido efectivamente devueltas y están listas para ser admiradas nuevamente, ejecutamos consultas que actúan como un recuento de asistencia:

```sql
select  count(*) departamentos
from departamentos; select  count(*) empleados from empleados;
```

Estas consultas nos confirman que '**_DEPARTAMENTOS_**' y '**_EMPLEADOS_**' no solo han sido recuperadas, sino que están completas, listas para contribuir una vez más a la rica narrativa de nuestra base de datos. Es un recordatorio poderoso de la capacidad de recuperación y renovación, donde incluso en el mundo de los datos, lo que una vez se consideró perdido puede ser redescubierto y restaurado, como las capas de pintura ocultas bajo la superficie de un cuadro, esperando ser reveladas de nuevo al mundo.

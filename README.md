# Giselle-Lopez

MODELO MVC

En la ingeniería de software, usamos patrones de diseño como soluciones reutilizables para un problema común, un patrón es como una plantilla de cómo resolver un problema. 
El Modelo-Vista-Controlador (MVC) es un patrón de diseño de software que divide el programa o la aplicación web relacionada en tres elementos o componentes interconectados. Cada uno de estos componentes se construye para manejar aspectos específicos de desarrollo de una aplicación. Este patrón es uno de los marcos de trabajo de desarrollo web estándar de la industria que se utiliza con más frecuencia. 
MVC se utiliza ampliamente en una variedad de los principales lenguajes de programación y es la arquitectura más popular para la construcción de servidores web complejos. Es utilizado por muchos marcos de trabajo e implementado en casi todas las aplicaciones web modernas. 
El modelo contiene información básica de la aplicación. Incluye datos, reglas de validación, acceso a datos y lógica de agregación
•	El Modelo que contiene una representación de los datos que maneja el sistema, su lógica de negocio, y sus mecanismos de persistencia.
•	La Vista, o interfaz de usuario, que compone la información que se envía al cliente y los mecanismos interacción con éste.
•	El Controlador, que actúa como intermediario entre el Modelo y la Vista, gestionando el flujo de información entre ellos y las transformaciones para adaptar los datos a las necesidades de cada uno
El Modelo es responsable de:
•	Acceder a la capa de almacenamiento de datos. Lo ideal es que el modelo sea independiente del sistema de almacenamiento.
•	Define las reglas de negocio (la funcionalidad del sistema). Un ejemplo de regla puede ser: "Si la mercancía pedida no está en el almacén, consultar el tiempo de entrega estándar del proveedor".
•	Lleva un registro de las vistas y controladores del sistema.
•	Si estamos ante un modelo activo, notificará a las vistas los cambios que en los datos pueda producir un agente externo (por ejemplo, un fichero por lotes  que actualiza los datos, un temporizador que desencadena una inserción, etc.).

El Controlador es responsable de:
•	 Recibe los eventos de entrada (un clic, un cambio en un campo de texto, etc.).
•	Contiene reglas de gestión de eventos, del tipo "SI Evento Z, entonces Acción W". Estas acciones pueden suponer peticiones al modelo o a las vistas. Una de estas peticiones a las vistas puede ser una llamada al método "Actualizar ()". Una petición al modelo puede ser "Obtener_tiempo_de_entrega (nueva_orden_de_venta)". 

Las Vistas son responsables de:
•	Recibir datos del modelo y los muestra al usuario.
•	Tienen un registro de su controlador asociado (normalmente porque además lo instancia).
•	Pueden dar el servicio de "Actualización ()", para que sea invocado por el controlador o por el modelo (cuando es un modelo activo que informa de los cambios en los datos producidos por otros agentes).

CARACTERISTICAS DEL PATRÓN DE DISEÑO

El patrón MVC lo podrías ver de la siguiente manera:

 
Por lo cual cuando el usuario realiza una operación lo hace a través del controlador el cual realiza operaciones sobre el modelo y luego notifica a la vista para que actualice la información que vemos en pantalla, Cuando el modelo es actualizado este puede arrojar eventos que notifican a la vista para que se actualice es por eso que existe la línea punteada entre ambos.
La vista tiene una relación indirecta con el controlador debido a que existen implementaciones de MVC en las cuales la vista no tiene acceso al Modelo por lo cual toda la información la obtiene a través del controlador.
En la siguiente imagen se muestra cómo se relacionan:
 
Pasos
1.	El usuario realiza una operación en la pantalla como presionar un botón.
2.	El controlador recibe la petición de realizar una acción
3.	El modelo es actualiza por medio del controlador el cual puede agregar, actualizar o borrar información.
4.	La vista es actualizada con los cambios realizados al modelo.
5.	El usuario recibe las actualizaciones en pantalla.
6.	FIN.

Muchas veces necesitamos utilizar parámetros entre distintos métodos de un mismo formulario web, o incluso entre formularios distintos. Una forma dinámica de realizar este intercambio de datos es la utilización del ViewState y del espacio de sesión.

Antes de embarcarnos a almacenar datos y datos en estos dos espacios de memoria, hay que dejar claras dos cosas:

El ViewState se intercambia continuamente entre cliente y servidor, por lo que es aconsejable no sobrecargarlo. Al aumentar el ViewState, aumentará el tamaño de la página, y por consiguiente, el tráfico de datos y la velocidad de carga de la misma.
La sesión se almacena en servidor, por lo que si nuestro sitio web posee una carga de datos moderada – alta, el rendimiento del mismo se verá penalizado. Nuevamente, es aconsejable utilizar con cautela este espacio de memoria.
Teniendo claros estos dos conceptos, utilizaremos el ViewState para almacenar datos locales (de una misma página) y la sesión para pasar parámetros entre distintas páginas sin hacer uso de la QueryString. Para los profanos en la materia, explicaremos que la QueryString es la colección de parámetros que pueden, opcionalmente, acompañar a la dirección de la página. Éstos parámetros ofrecen información directamente recopilable por nuestro formulario web, pero cuenta con la desventaja de que es visible para el usuario.


VISTA

<?php 

require_once 'model/note.php';

class noteController{
	public $page_title;
	public $view;

	public function __construct() {
		$this->view = 'list_note';
		$this->page_title = '';
		$this->noteObj = new Note();
	}

	/* List all notes */
	public function list(){
		$this->page_title = 'Listado de notas';
		return $this->noteObj->getNotes();
	}

	/* Load note for edit */
	public function edit($id = null){
		$this->page_title = 'Editar nota';
		$this->view = 'edit_note';
		/* Id can from get param or method param */
		if(isset($_GET["id"])) $id = $_GET["id"];
		return $this->noteObj->getNoteById($id);
	}

	/* Create or update note */
	public function save(){
		$this->view = 'edit_note';
		$this->page_title = 'Editar nota';
		$id = $this->noteObj->save($_POST);
		$result = $this->noteObj->getNoteById($id);
		$_GET["response"] = true;
		return $result;
	}

	/* Confirm to delete */
	public function confirmDelete(){
		$this->page_title = 'Eliminar nota';
		$this->view = 'confirm_delete_note';
		return $this->noteObj->getNoteById($_GET["id"]);
	}

	/* Delete */
	public function delete(){
		$this->page_title = 'Listado de notas';
		$this->view = 'delete_note';
		return $this->noteObj->deleteNoteById($_POST["id"]);
	}

}

?>



MODELO
<?php 

require_once 'config/config.php';

class Db {

	private $host;
	private $db;
	private $user;
	private $pass;
	public $conection;

	public function __construct() {		

		$this->host = constant('DB_HOST');
		$this->db = constant('DB');
		$this->user = constant('DB_USER');
		$this->pass = constant('DB_PASS');

		try {
           $this->conection = new PDO('mysql:host='.$this->host.'; dbname='.$this->db, $this->user, $this->pass);
        } catch (PDOException $e) {
            echo $e->getMessage();
            exit();
        }

	}

}

?>

CONTROLADOR

<?php 

require_once 'model/note.php';

class noteController{
	public $page_title;
	public $view;

	public function __construct() {
		$this->view = 'list_note';
		$this->page_title = '';
		$this->noteObj = new Note();
	}

	/* List all notes */
	public function list(){
		$this->page_title = 'Listado de notas';
		return $this->noteObj->getNotes();
	}

	/* Load note for edit */
	public function edit($id = null){
		$this->page_title = 'Editar nota';
		$this->view = 'edit_note';
		/* Id can from get param or method param */
		if(isset($_GET["id"])) $id = $_GET["id"];
		return $this->noteObj->getNoteById($id);
	}

	/* Create or update note */
	public function save(){
		$this->view = 'edit_note';
		$this->page_title = 'Editar nota';
		$id = $this->noteObj->save($_POST);
		$result = $this->noteObj->getNoteById($id);
		$_GET["response"] = true;
		return $result;
	}

	/* Confirm to delete */
	public function confirmDelete(){
		$this->page_title = 'Eliminar nota';
		$this->view = 'confirm_delete_note';
		return $this->noteObj->getNoteById($_GET["id"]);
	}

	/* Delete */
	public function delete(){
		$this->page_title = 'Listado de notas';
		$this->view = 'delete_note';
		return $this->noteObj->deleteNoteById($_POST["id"]);
	}

}

?>


La arquitectura MVC separa la lógica de negocio (el modelo) y la presentación (la vista) por lo que se consigue un mantenimiento más sencillo de las aplicaciones. Si por ejemplo una misma aplicación debe ejecutarse tanto en un navegador estándar como un un navegador de un dispositivo móvil, solamente es necesario crear una vista nueva para cada dispositivo; manteniendo el controlador y el modelo original. El controlador se encarga de aislar al modelo y a la vista de los detalles del protocolo utilizado para las peticiones (HTTP, consola de comandos, email, etc.). El modelo se encarga de la abstracción de la lógica relacionada con los datos, haciendo que la vista y las acciones sean independientes de, por ejemplo, el tipo de gestor de bases de datos utilizado por la aplicación.

>Las capas de la arquitectura MVC<
Para poder entender las ventajas de utilizar el patrón MVC, se va a transformar una aplicación simple realizada con PHP en una aplicación que sigue la arquitectura MVC. Un buen ejemplo para ilustrar esta explicación es el de mostrar una lista con las últimas entradas o artículos de un blog.

>Programación simple<
Utilizando solamente PHP normal y corriente, el script necesario para mostrar los artículos almacenados en una base de datos 

>Un script simple<

<?php

// Conectar con la base de datos y seleccionarla
$conexion = mysql_connect('localhost', 'miusuario', 'micontrasena');
mysql_select_db('blog_db', $conexion);

// Ejecutar la consulta SQL
$resultado = mysql_query('SELECT fecha, titulo FROM articulo', $conexion);

?>

<html>
  <head>
    <title>Listado de Artículos</title>
  </head>
  <body>
   <h1>Listado de Artículos</h1>
   <table>
     <tr><th>Fecha</th><th>Titulo</th></tr>
<?php
// Mostrar los resultados con HTML
while ($fila = mysql_fetch_array($resultado, MYSQL_ASSOC))
{
echo "\t<tr>\n";
printf("\t\t<td> %s </td>\n", $fila['fecha']);
printf("\t\t<td> %s </td>\n", $fila['titulo']);
echo "\t</tr>\n";
}
?>
    </table>
  </body>
</html>

<?php

// Cerrar la conexion
mysql_close($conexion);

?>




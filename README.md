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

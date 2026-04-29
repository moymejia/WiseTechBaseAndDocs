# Datatables 2.x

Documentacion actualizada segun el comportamiento actual de [intranet/php/wisetech/datatables.php]


# 1. Operaciones INTERNAS de la clase

  # 1.2 guardar_estado_datatables
    - Guarda en `solomoda_seguridad.datatables` por usuario y tabla.
    - Es para uso 'interno' en combinacion con activar_tabla de commnon.js. No deberia usarse fuera de esto 

  # 1.3 cargar_estado_datatables
    - Carga todos los estados de tablas del usuario actual.
    - Es para uso 'interno' en combinacion con activar_tabla de commnon.js. No deberia usarse fuera de esto 

# 2.  Metodo addTable 

  # 2.1 Parametros 
            ```php
            public function addTable(
                $result, 
                $PARAMETROS = [],         
                $style = "",
                $special_columns = [],
                $aligments = [],
                $hidden_columns = [],
                $idtabla = "tabla_datos"
            )
            ```
          - `$result`: resultado de consulta MySQL.
          - `$PARAMETROS`: configuracion general de DataTables 2.
          - `$style`: estilo inline para `<table>`.
          - `$special_columns`: Para agregar columnas que realicen alguna accion/opercion particular 
          - `$aligments`: alineacion por columna (`left`, `center`, `right`).
          - `$hidden_columns`: arreglo de nombres de columna a ocultar.
          - `$idtabla`: id final de la tabla. 

  # 2.2 Configuracion aceptada en el array $PARAMETROS
          - PARAMETROS es un arreglo para configurar datatables 2, 
          - Si un valor es falso , entonces se puede omitir (ver ejemplo de uso mas adelante)
          - Puede tener cualquier de los siguientes valores
              - `columncontrol` (bool)
              - `responsive` (bool)
              - `colreorder` (bool)
              - `select` (bool)
              - `buttons` (bool)
              - `paging` (bool)
              - `ordering` (bool)
              - `order` (bool)
              - `reset` (bool)
              - `rowgroup` (false o nombre exacto de columna)
              - `acciones` (bool)
              - `titulotabla` (string)
              - `filename` (string)

          - Con los valores que se incluyan en el array PARAMETROS, se crean valores de tipo data- que se incluyen en el 
            encabezado de la tabla con los valores indicados en el array. 
              - `data-conf-columncontrol`
              - `data-conf-rowgroup`
              - `data-conf-titulotabla`
              - `data-conf-filename`
              - `data-conf-responsive`
              - `data-conf-colreorder`
              - `data-conf-select`
              - `data-conf-buttons`
              - `data-conf-paging`
              - `data-conf-ordering`
              - `data-conf-noorder`
              - `data-conf-reset`

  # 2.4 Comportamiento adicional
            - Si `acciones = true`, agrega columna `Acciones` con boton `Editar`.
            - Si una columna esta en `$hidden_columns`, no se pinta en `<thead>` ni en `<tbody>`.
            - Si existe plantilla en `$special_columns[columna]`, reemplaza placeholders con valores de la fila.
            - Evita IDs de tabla duplicados dentro de la misma instancia (`$IDS`).

  # 2.5 Ejemplo de uso real

        ```php
        -- NOTESE QUE $CONFIGURACION no incluye todos los posibles valores 
          por lo tanto lo que no se colocan se asumen como false 

        $CONFIGURACION = [
            'columncontrol' => true, 
            'responsive'    => true,
            'select'        => true,
            'buttons'       => true,
            'ordering'      => true,
            'order'         => true,
            'reset'         => true,
            'acciones'      => true,
            'titulotabla'   => 'Listado de marcas',
            'filename'      => 'Marcas'
        ];

        - Aca agregaremos una columna adicional que mostrara el estado del registro

        $SPECIAL_COLUMNS = [
            'estado' => '<span class="badge">[estado]</span>'
        ];

        -- El contenido de las  columnas de 'codigo' y 'estado' se mostrara centrado dentro de sus td
        $ALIGMENTS = [
            'codigo' => 'center',
            'estado' => 'center'
        ];
        -- Se oculta la columna de id
        $HIDDEN_COLUMNS = ['id_marca'];


        -- Instanciamos la clase y llamamos al metodo con todos los parametros configurados 

        $_DATATABLES = new datatables();
        $tabla =  $DATATABLES->addTable(
            $result,
            $CONFIGURACION,
            '',
            $SPECIAL_COLUMNS,
            $ALIGMENTS,
            $HIDDEN_COLUMNS,
            'tabla_marca'
        );
        ```


---

# 3. Metodos de construccion de reportes

    Ademas de `addTable`, la clase incluye utilidades para armar HTML de reporte:

  - `addTitle($text)`: Agrega un titulo principal en formato `<h2>`, centrado.
  - `addSubTitle($text)`: Agrega un subtitulo en formato `<h4>`.
  - `addBreakLine($cantidad = 1)`: Inserta una o varias etiquetas `<br>` para separar bloques.
  - `addParagraph($text)`: Agrega un parrafo en formato `<p>`.
  - `addText($text)`: Agrega texto en linea en formato `<span>`.
  - `addLogo($url)`: Inserta una imagen (generalmente logo) alineada a la izquierda.
  - `addTableToReport(...)`: Agrega una tabla al reporte reutilizando internamente `addTable`.
  - `getReport()`: Retorna todo el HTML acumulado del reporte.
  - `reset()`: Limpia el contenido acumulado del reporte para empezar desde cero.

Estos metodos no dependen de `operacion`; se usan de forma directa al instanciar la clase en PHP.

---

# 4. Notas practicas

- Si no envias `idtabla`, se usa `tabla_datos`.
- Para evitar conflicto de IDs en la misma respuesta, usa un `idtabla` distinto por tabla.

# 5 Ejemplo de uso 
  ```php
          $DATA        = [];
        $result      = mysql::getresult("SELECT idmarca, '' boton1, nombre, '' boton2, estado  FROM marca ORDER BY idmarca DESC");
        // $DATA['tabla_marca'] =  $this->construir_tabla_marca($result); FORMA ANTIGUA DE USAR DATATAVLES  
        $CONFIG_TABLA = [];
        // $CONFIG_TABLA['columncontrol'] = false;
        $CONFIG_TABLA['responsive']    = true;
        $CONFIG_TABLA['colreorder']    = true;
        //$CONFIG_TABLA['select']        = false;
        $CONFIG_TABLA['buttons']       = true;
        $CONFIG_TABLA['paging']        = true;
        $CONFIG_TABLA['ordering']      = true;
        $CONFIG_TABLA['order']         = true;
        // $CONFIG_TABLA['rowgroup']      = false;
        $CONFIG_TABLA['reset']         = true;
        $CONFIG_TABLA['acciones']      = true;
        $CONFIG_TABLA['titulotabla']   = 'Marcas';
        $CONFIG_TABLA['filename']      = 'Marcas';
        // Ejemplo de configuracion heredadas desde reportes 
        $style = "border-collapse:collapse;";  // Estilos CSS para la tabla
        $aligments = ['nombre' => 'right', 'estado' => 'center'];
        // $special_columns = [ 'Estado Actual' => '<span class="badge badge-[estado]">[estado]</span>'];
        $special_columns['boton1'] = '<button class="btn btn-sm btn-primary waves-effect waves-light" type="button" onclick="alert(\'idmarca:[idmarca]\')"><span class="btn-label"><i class="far fa-edit"></i></span>Editar</button>';
        $special_columns['boton2'] = '<button class="btn btn-sm btn-primary waves-effect waves-light" type="button" onclick="alert(\'nombre:[nombre]\')"><span class="btn-label"><i class="far fa-edit"></i></span>Editar</button>';
        $hidden_columns_adicionales = ['idmarca','estado'];  // Columnas a ocultar (además de idmarca ya en CONFIG_TABLA)
        // Ejemplo de uso de los metodos heredados desde reportes
        $_DATATABLES = new datatables();
        $_DATATABLES->addTitle('TITULO');
        $_DATATABLES->addSubTitle('SUBTITULO');
        $_DATATABLES->addBreakLine(1);
        $_DATATABLES->addParagraph('PARRAFO DE EJEMPLO PARA DESCRIPCION DEL REPORTE O INSTRUCCIONES');
        $_DATATABLES->addBreakLine(1);
        $_DATATABLES->addTableToReport($result, $CONFIG_TABLA, $style, $special_columns, $aligments, $hidden_columns_adicionales, 'tabla_marca');
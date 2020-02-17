# Generalidades
- Considero que la clase "Parse" es para archivos de un tamaño pequeño, ya que se tendría que utilizar una estrategía de buffer y streams diferentes.
https://docs.oracle.com/javase/tutorial/essential/io/file.html

![File I/O Methods Arranged from Less Complex to More Complex](https://docs.oracle.com/javase/tutorial/figures/essential/io-fileiomethods.gif)

- Ademas de la etiqueta "Thread safety" y "synchronized" para el bloqueo transitorio de los archivos y las operaciones I/O, con el fin de tener archivos eventualmente-consistentes o consistentes.

# Codigo 
- Renombrar variables para legibilidad y mantebilidad.

# Cerrar las conexiones
- No esta cerrando las conexiones de lectura y escritura de los archivos
- Agregar bloques try, catch and finally para cerrar los streams; ya que no se cierran.


# Validaciones
- Constructor con validación del archivo a "parsear" no puede ser "null"

```java
if( file != null) {
   this.file = file
}
else{
   throw new IllegalArgumentException("File to parse can not be null");
}    
```

- Verificar la existencia del archivo antes de hacer hacer operaciones de escritura o try-catch para FileNotFoundException.
```java  
myObj.exists()
```

- Lo anterior para asegurarse que la instancia de la variable no es nula antes de hacer alguna llamado a una función.

- Archivos grandes no podrían ser "parseados" 
  
# Nombre del archivo
- Esta bien el uso de java.io.File, para evitar inconsistencias de nomenclatura de los sistemas operativos o de las plataformas.

# Concurrencia/Rendimiento
- Ya que la clase tiene la palabra clave "synchronized" asumo que van haber operaciones concurrentes de lectura/escritura de un archivo grande o multiples archivos o escritura. Lo que implica que se debe "bloquear" un bloque crítico de código para no afectar el rendimiento. Se podría agregar synchronized a los metodos getContent, getContentUnicode, saveContent dentro de la funcion no en el encabezado. Ya que las operaciones de I/O no es solo de "read-only" u objetos inmutables, para evitar corrupción o comportamientos inesperados.

# Streams y Buffers
- Hay concatenación en lugar de usar StringBuilders
- El redimiento de las operaciones de I/O dependen mucho de la estrategía del uso de los Buffers en éste caso: StringBuilder en lugar de "String 
output" en los loops de las funciones getContent* y saveContent, por ejemplo para escribir:


```java
// Lectura
File f = new File("Path");
FileReader fr = new FileReader(f);
BufferedReader br = new BufferedReader(fr);

// Escritura
File f = new File("Path");
FileWriter fw = new FileWriter(f);
BufferedWriter bw = new BufferedWriter(fw);

```
## Ejemplo de scritura

```java
    FileOutputStream writeStream = null;
    BufferedOutputStream bufferedWriteStream = null;
    try {
        if (content != null && content.trim().length() > 0) {
            writeStream = new FileOutputStream(file);
            bufferedWriteStream = new BufferedOutputStream(writeStream);
            bufferedWriteStream.write(content.getBytes());
        }
    } finally {
        if (writeStream != null && bufferedWriteStream != null) {
            writeStream.close();
            bufferedWriteStream.close();
        }
    }
```

# Metodo getContentWithoutUnicode
- InputStream.read() retorna el próximo Byte, 2 elevado a la 8  = 256, entonces podría representar del 0 al 255 equivalente en Unicode. Y como solo agregaría los menores a 0x80 = 128; no estarían todos.

"UTF-8 utiliza 1 byte para representar caracteres en el set ASCII, dos bytes para caracteres en otros bloques alfabéticos y tres bytes para el resto del BMP. Para los caracteres complementarios se utilizan 4 bytes. UTF-16 utiliza 2 bytes para cualquier carácter en el BMP y 4 bytes para los caracteres complementarios"

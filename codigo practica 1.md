import java.io.*;
import java.util.ArrayList;
import java.util.List;
import java.util.Scanner;

// Clase que representa un libro
class Libro {
    String titulo;
    String autor;
    double precio;
    int anio;
    String imagen;

    // Constructor
    public Libro(String titulo, String autor, double precio, int anio, String imagen) {
        this.titulo = titulo;
        this.autor = autor;
        this.precio = precio;
        this.anio = anio;
        this.imagen = imagen;
    }

    // Método para convertir el libro a una cadena para guardar en el archivo
    @Override
    public String toString() {
        return "Título: " + titulo + "\n" +
               "Autor: " + autor + "\n" +
               "Precio: " + precio + "\n" +
               "Año: " + anio + "\n" +
               "Imagen: " + imagen + "\n" +
               "\n";
    }

    // Método estático para crear un libro a partir de una cadena
    public static Libro fromString(String str) {
        String[] partes = str.split("\n");
        return new Libro(partes[0].substring(8), // Extraer título
                         partes[1].substring(7), // Extraer autor
                         Double.parseDouble(partes[2].substring(8)), // Extraer precio
                         Integer.parseInt(partes[3].substring(6)), // Extraer año
                         partes[4].substring(8)); // Extraer imagen
    }
}

// Clase que gestiona la colección de libros
class Biblioteca {
    private List<Libro> libros;
    private String archivo;

    // Constructor
    public Biblioteca(String archivo) {
        this.libros = new ArrayList<>();
        this.archivo = archivo;
        cargarLibros();
    }

    // Cargar libros desde el archivo
    private void cargarLibros() {
        try (BufferedReader br = new BufferedReader(new FileReader(archivo))) {
            StringBuilder builder = new StringBuilder();
            String linea;
            while ((linea = br.readLine()) != null) {
                builder.append(linea).append("\n");
                if (linea.startsWith("Imagen: ")) { // Final de un libro
                    libros.add(Libro.fromString(builder.toString()));
                    builder.setLength(0); // Reiniciar StringBuilder
                }
            }
        } catch (IOException e) {
            System.out.println("Error al cargar los libros: " + e.getMessage());
        }
    }

    // Guardar libros en el archivo actual
    public void guardarLibros() {
        guardarLibros(archivo);
    }

    // Guardar libros en un archivo específico
    public void guardarLibros(String archivo) {
        try (BufferedWriter bw = new BufferedWriter(new FileWriter(archivo))) {
            for (Libro libro : libros) {
                bw.write(libro.toString());
            }
        } catch (IOException e) {
            System.out.println("Error al guardar los libros: " + e.getMessage());
        }
    }

    // Agregar un nuevo libro
    public void agregarLibro(Libro libro) {
        libros.add(libro);
        guardarLibros();
    }

    // Modificar un libro existente
    public void modificarLibro(int indice, Libro libro) {
        if (indice >= 0 && indice < libros.size()) {
            libros.set(indice, libro);
            guardarLibros();
        }
    }

    // Eliminar un libro
    public void eliminarLibro(int indice) {
        if (indice >= 0 && indice < libros.size()) {
            libros.remove(indice);
            guardarLibros();
        }
    }

    // Listar todos los libros
    public List<Libro> listarLibros() {
        return libros;
    }

    // Buscar un libro por título
    public List<Libro> buscarLibro(String criterio) {
        List<Libro> resultados = new ArrayList<>();
        for (Libro libro : libros) {
            if (libro.titulo.toLowerCase().contains(criterio.toLowerCase())) {
                resultados.add(libro);
            }
        }
        return resultados;
    }

    // Eliminar todos los libros
    public void eliminarTodos() {
        libros.clear();
        guardarLibros();
    }
}

// Clase principal de la aplicación
public class App {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        Biblioteca biblioteca = new Biblioteca("libros.txt");

        while (true) {
            System.out.println("\n1. Agregar libro");
            if (biblioteca.listarLibros().isEmpty()) {
                System.out.println("2. Listar libros");
                System.out.println("3. Modificar libro");
                System.out.println("4. Eliminar libro");
                System.out.println("5. Buscar libro");
            } else {
                System.out.println("2. Listar libros");
                System.out.println("3. Modificar libro");
                System.out.println("4. Eliminar libro");
                System.out.println("5. Buscar libro");
            }
            System.out.println("6. Eliminar todos los libros");
            System.out.println("7. Guardar como");
            System.out.println("8. Salir");

            System.out.print("Elige una opción: ");
            int opcion = scanner.nextInt();
            scanner.nextLine(); // Limpiar el buffer

            switch (opcion) {
                case 1: // Agregar libro
                    System.out.print("Título: ");
                    String titulo = scanner.nextLine();
                    System.out.print("Autor: ");
                    String autor = scanner.nextLine();
                    System.out.print("Precio: ");
                    double precio = scanner.nextDouble();
                    System.out.print("Año: ");
                    int anio = scanner.nextInt();
                    scanner.nextLine(); // Limpiar el buffer
                    System.out.print("Ruta de la imagen: ");
                    String imagen = scanner.nextLine();
                    biblioteca.agregarLibro(new Libro(titulo, autor, precio, anio, imagen));
                    break;

                case 2: // Listar libros
                    if (biblioteca.listarLibros().isEmpty()) {
                        System.out.println("No hay libros para listar.");
                    } else {
                        List<Libro> libros = biblioteca.listarLibros();
                        for (int i = 0; i < libros.size(); i++) {
                            System.out.println(i + ": " + libros.get(i).titulo + " por " + libros.get(i).autor);
                        }
                    }
                    break;

                case 3: // Modificar libro
                    if (biblioteca.listarLibros().isEmpty()) {
                        System.out.println("No hay libros para modificar.");
                    } else {
                        System.out.print("Índice del libro a modificar: ");
                        int indiceMod = scanner.nextInt();
                        scanner.nextLine(); // Limpiar el buffer
                        System.out.print("Nuevo título: ");
                        String nuevoTitulo = scanner.nextLine();
                        System.out.print("Nuevo autor: ");
                        String nuevoAutor = scanner.nextLine();
                        System.out.print("Nuevo precio: ");
                        double nuevoPrecio = scanner.nextDouble();
                        System.out.print("Nuevo año: ");
                        int nuevoAnio = scanner.nextInt();
                        scanner.nextLine(); // Limpiar el buffer
                        System.out.print("Nueva ruta de la imagen: ");
                        String nuevaImagen = scanner.nextLine();
                        biblioteca.modificarLibro(indiceMod, new Libro(nuevoTitulo, nuevoAutor, nuevoPrecio, nuevoAnio, nuevaImagen));
                    }
                    break;

                case 4: // Eliminar libro
                    if (biblioteca.listarLibros().isEmpty()) {
                        System.out.println("No hay libros para eliminar.");
                    } else {
                        System.out.print("Índice del libro a eliminar: ");
                        int indiceEliminar = scanner.nextInt();
                        biblioteca.eliminarLibro(indiceEliminar);
                    }
                    break;

                case 5: // Buscar libro
                    if (biblioteca.listarLibros().isEmpty()) {
                        System.out.println("No hay libros para buscar.");
                    } else {
                        System.out.print("Título del libro: ");
                        String criterio = scanner.nextLine();
                        List<Libro> resultados = biblioteca.buscarLibro(criterio);
                        if (resultados.isEmpty()) {
                            System.out.println("No se encontraron libros que coincidan con el criterio.");
                        } else {
                            for (Libro libro : resultados) {
                                System.out.println("* " + libro.titulo);
                            }
                        }
                    }
                    break;

                case 6: // Eliminar todos los libros
                    biblioteca.eliminarTodos();
                    System.out.println("Todos los libros han sido eliminados.");
                    break;

                case 7: // Guardar como
                    System.out.print("Introduce la nueva ruta y nombre del archivo: ");
                    String nuevaRuta = scanner.nextLine();
                    biblioteca.guardarLibros(nuevaRuta);
                    System.out.println("Datos guardados en: " + nuevaRuta);
                    break;

                case 8: // Salir
                    scanner.close();
                    return;

                default:
                    System.out.println("Opción no válida.");
            }
        }
    }
}

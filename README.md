#include <iostream>
#include <string>
#include <vector>
#include <cstdlib>
#include <ctime>
#include <chrono>
#include <thread>

using namespace std;

// ============================ CLASES PRINCIPALES ============================

class Credito {
private:
    string nombre;
    string apellido;
    string codigoAfiliacion;
    string categoria; // "Productor", "Musico", "Compositor"

public:
    Credito(string n, string a, string c, string cat) 
        : nombre(n), apellido(a), codigoAfiliacion(c), categoria(cat) {}
    
    // Getters
    string getNombre() const { return nombre; }
    string getApellido() const { return apellido; }
    string getCodigo() const { return codigoAfiliacion; }
    string getCategoria() const { return categoria; }
};

class Cancion {
private:
    string nombre;
    int identificador;
    int duracion; // en segundos
    string rutaAudio128;
    string rutaAudio320;
    int reproducciones;

public:
    vector<Credito> creditos;
    
    Cancion(string n, int id, int dur, string r128, string r320) 
        : nombre(n), identificador(id), duracion(dur), 
          rutaAudio128(r128), rutaAudio320(r320), reproducciones(0) {}
    
    void agregarCredito(const Credito& credito) {
        creditos.push_back(credito);
    }
    
    void reproducir() { reproducciones++; }
    
    // Getters
    string getNombre() const { return nombre; }
    int getId() const { return identificador; }
    int getDuracion() const { return duracion; }
    string getRuta128() const { return rutaAudio128; }
    string getRuta320() const { return rutaAudio320; }
    int getReproducciones() const { return reproducciones; }
    
    // Sobrecarga de operadores
    bool operator==(const Cancion& otra) const {
        return identificador == otra.identificador;
    }
};

class Album {
private:
    string nombre;
    int identificador;
    string fechaLanzamiento;
    string selloDisquero;
    string rutaPortada;
    float puntuacion;

public:
    vector<Cancion> canciones;
    vector<string> generos;
    
    Album(string n, int id, string fecha, string sello, string ruta, float punt)
        : nombre(n), identificador(id), fechaLanzamiento(fecha), 
          selloDisquero(sello), rutaPortada(ruta), puntuacion(punt) {}
    
    void agregarCancion(const Cancion& cancion) {
        canciones.push_back(cancion);
    }
    
    void agregarGenero(const string& genero) {
        if (generos.size() < 4) {
            generos.push_back(genero);
        }
    }
    
    // Getters
    string getNombre() const { return nombre; }
    int getId() const { return identificador; }
    string getRutaPortada() const { return rutaPortada; }
    vector<Cancion>& getCanciones() { return canciones; }
    const vector<Cancion>& getCanciones() const { return canciones; } // Añadido: versión const
    
    // Sobrecarga de operador <<
    friend ostream& operator<<(ostream& os, const Album& album);
};

ostream& operator<<(ostream& os, const Album& album) {
    os << "Album: " << album.nombre << " (" << album.fechaLanzamiento << ")";
    os << " - Puntuacion: " << album.puntuacion;
    return os;
}

class Artista {
private:
    int codigo;
    int edad;
    string pais;
    int seguidores;
    int posicionTendencias;

public:
    vector<Album> albumes;
    
    Artista(int c, int e, string p, int s, int pos)
        : codigo(c), edad(e), pais(p), seguidores(s), posicionTendencias(pos) {}
    
    void agregarAlbum(const Album& album) {
        albumes.push_back(album);
    }
    
    // Getters
    int getCodigo() const { return codigo; }
    vector<Album>& getAlbumes() { return albumes; }
    const vector<Album>& getAlbumes() const { return albumes; } // Añadido: versión const
};

class MensajePublicitario {
private:
    string contenido;
    string categoria; // "C", "B", "AAA"
    int prioridad;

public:
    MensajePublicitario(string cont, string cat) 
        : contenido(cont), categoria(cat) {
        if (categoria == "C") prioridad = 1;
        else if (categoria == "B") prioridad = 2;
        else if (categoria == "AAA") prioridad = 3;
        else prioridad = 1;
    }
    
    // Getters
    string getContenido() const { return contenido; }
    string getCategoria() const { return categoria; }
    int getPrioridad() const { return prioridad; }
};

class Usuario {
protected:
    string nickname;
    string tipoMembresia; // "estandar" o "premium"
    string ciudad;
    string pais;
    string fechaInscripcion;
    int iteracionesConsumidas;
    int memoriaConsumida;
    int reproduccionesRealizadas; // Nuevo: contador de reproducciones

public:
    Usuario(string nick, string tipo, string ciu, string pa, string fecha)
        : nickname(nick), tipoMembresia(tipo), ciudad(ciu), 
          pais(pa), fechaInscripcion(fecha), iteracionesConsumidas(0), 
          memoriaConsumida(0), reproduccionesRealizadas(0) {}
    
    virtual ~Usuario() {}
    
    // Getters
    string getNickname() const { return nickname; }
    string getTipoMembresia() const { return tipoMembresia; }
    int getIteraciones() const { return iteracionesConsumidas; }
    int getMemoria() const { return memoriaConsumida; }
    int getReproduccionesRealizadas() const { return reproduccionesRealizadas; } // Nuevo getter
    
    void setIteraciones(int iter) { iteracionesConsumidas = iter; }
    void setMemoria(int mem) { memoriaConsumida = mem; }
    void incrementarReproducciones() { reproduccionesRealizadas++; } // Nuevo método
    
    virtual void mostrarMetricas() {
        cout << "=== METRICAS DE CONSUMO ===" << endl;
        cout << "Iteraciones requeridas: " << iteracionesConsumidas << endl;
        cout << "Memoria consumida: " << memoriaConsumida << " bytes" << endl;
        cout << "Reproducciones realizadas: " << reproduccionesRealizadas << endl; // Nueva métrica
        cout << "============================" << endl;
    }
    
    // Nuevo método para verificar límite de reproducciones
    virtual bool puedeReproducir() {
        return true; // Por defecto, siempre puede reproducir
    }
    
    // Nuevo método para cambiar membresía
    virtual void cambiarMembresia(const string& nuevaMembresia) {
        tipoMembresia = nuevaMembresia;
        cout << "Membresia cambiada a: " << tipoMembresia << endl;
    }
};

class UsuarioEstandar : public Usuario {
private:
    int cancionesReproducidas;
    static const int LIMITE_REPRODUCCIONES = 10; // Límite para usuarios estándar

public:
    UsuarioEstandar(string nick, string ciu, string pa, string fecha)
        : Usuario(nick, "estandar", ciu, pa, fecha), cancionesReproducidas(0) {}
    
    bool debeMostrarPublicidad() {
        cancionesReproducidas++;
        return (cancionesReproducidas % 2 == 0);
    }
    
    // Sobrescribir el método para verificar límite
    bool puedeReproducir() override {
        if (getReproduccionesRealizadas() >= LIMITE_REPRODUCCIONES) {
            cout << "¡Has alcanzado el limite de " << LIMITE_REPRODUCCIONES << " reproducciones!" << endl;
            cout << "Actualiza a Premium para reproduccion ilimitada." << endl;
            return false;
        }
        return true;
    }
    
    // Mostrar información del límite
    void mostrarInfoLimite() {
        int reproduccionesRestantes = LIMITE_REPRODUCCIONES - getReproduccionesRealizadas();
        cout << "Reproducciones restantes: " << reproduccionesRestantes << "/" << LIMITE_REPRODUCCIONES << endl;
    }
};

class UsuarioPremium : public Usuario {
private:
    vector<Cancion> favoritos;
    vector<string> usuariosSeguidos;

public:
    UsuarioPremium(string nick, string ciu, string pa, string fecha)
        : Usuario(nick, "premium", ciu, pa, fecha) {}
    
    bool agregarFavorito(const Cancion& cancion) {
        if (favoritos.size() >= 10000) {
            cout << "Limite de favoritos alcanzado (10000 canciones)" << endl;
            return false;
        }
        
        // Verificar si ya existe
        for (const auto& fav : favoritos) {
            if (fav == cancion) {
                cout << "La cancion ya esta en favoritos" << endl;
                return false;
            }
        }
        
        favoritos.push_back(cancion);
        cout << "Cancion agregada a favoritos!" << endl;
        return true;
    }
    
    bool eliminarFavorito(int idCancion) {
        for (auto it = favoritos.begin(); it != favoritos.end(); ++it) {
            if (it->getId() == idCancion) {
                favoritos.erase(it);
                cout << "Cancion eliminada de favoritos!" << endl;
                return true;
            }
        }
        cout << "Cancion no encontrada en favoritos" << endl;
        return false;
    }
    
    void seguirUsuario(const string& nickname) {
        usuariosSeguidos.push_back(nickname);
        cout << "Ahora sigues a: " << nickname << endl;
    }
    
    vector<Cancion>& getFavoritos() { return favoritos; }
    vector<string>& getUsuariosSeguidos() { return usuariosSeguidos; }
    
    void mostrarFavoritos() {
        cout << "=== MIS FAVORITOS ===" << endl;
        for (size_t i = 0; i < favoritos.size(); i++) {
            cout << (i+1) << ". " << favoritos[i].getNombre() 
                 << " (ID: " << favoritos[i].getId() << ")" << endl;
        }
        cout << "Total: " << favoritos.size() << " canciones" << endl;
    }
    
    // Los usuarios premium siempre pueden reproducir
    bool puedeReproducir() override {
        return true;
    }
};

// ============================ SISTEMA PRINCIPAL ============================

class UdeATunes {
private:
    vector<Usuario*> usuarios;
    vector<Artista> artistas;
    vector<MensajePublicitario> mensajesPublicitarios;
    Usuario* usuarioActual;
    int totalIteraciones;
    int totalMemoria;
    bool reproduccionActiva; // Nuevo: controlar estado de reproducción

public:
    UdeATunes() : usuarioActual(nullptr), totalIteraciones(0), totalMemoria(0), reproduccionActiva(false) {
        inicializarDatos();
    }
    
    ~UdeATunes() {
        for (auto usuario : usuarios) {
            delete usuario;
        }
    }

private:
    void inicializarDatos() {
        // Crear usuarios de ejemplo
        usuarios.push_back(new UsuarioEstandar("juan_estandar", "Medellin", "Colombia", "2024-01-15"));
        usuarios.push_back(new UsuarioPremium("maria_premium", "Bogota", "Colombia", "2024-02-20"));
        
        // Crear mensajes publicitarios de ejemplo
        mensajesPublicitarios.push_back(MensajePublicitario(
            "¡Descuento del 20% en audifonos!", "B"));
        mensajesPublicitarios.push_back(MensajePublicitario(
            "Suscribete a Premium por solo $19.900!", "AAA"));
        mensajesPublicitarios.push_back(MensajePublicitario(
            "Nuevo album disponible esta semana", "C"));
        
        // Crear artista y album de ejemplo
        Artista artista1(12345, 35, "Colombia", 50000, 1);
        
        Album album1("Lugar Secreto", 12, "2024-03-01", "Sello Indie", 
                    "/users/storage/claudialorelle/image/lugar secreto.png", 8.5);
        album1.agregarGenero("Pop");
        album1.agregarGenero("Rock");
        
        Cancion cancion1("A tu lado", 123451201, 240,
                        "/users/storage/claudialorelle/audio/a tu lado_128.ogg",
                        "/users/storage/claudialorelle/audio/a tu lado_320.ogg");
        
        Credito credito1("Carlos", "Gomez", "A123456789", "Compositor");
        cancion1.agregarCredito(credito1);
        
        album1.agregarCancion(cancion1);
        artista1.agregarAlbum(album1);
        artistas.push_back(artista1);
        
        // Calcular memoria inicial aproximada
        calcularMemoriaTotal();
    }
    
    void calcularMemoriaTotal() {
        totalMemoria = 0;
        
        // Memoria de usuarios
        for (const auto& usuario : usuarios) {
            totalMemoria += sizeof(*usuario);
        }
        
        // Memoria de artistas y sus contenidos
        for (const auto& artista : artistas) {
            totalMemoria += sizeof(artista);
            for (const auto& album : artista.getAlbumes()) {
                totalMemoria += sizeof(album);
                for (const auto& cancion : album.getCanciones()) {
                    totalMemoria += sizeof(cancion);
                }
            }
        }
        
        // Memoria de mensajes publicitarios
        for (const auto& mensaje : mensajesPublicitarios) {
            totalMemoria += sizeof(mensaje);
        }
    }
    
    // Nuevo método para cerrar sesión
    void cerrarSesion() {
        if (usuarioActual) {
            cout << "Cerrando sesion de " << usuarioActual->getNickname() << "..." << endl;
            usuarioActual = nullptr;
            reproduccionActiva = false;
        } else {
            cout << "No hay ninguna sesion activa" << endl;
        }
    }
    
    // Nuevo método para actualizar a premium
    void actualizarAPremium() {
        if (!usuarioActual) {
            cout << "Debe iniciar sesion primero" << endl;
            return;
        }
        
        if (usuarioActual->getTipoMembresia() == "premium") {
            cout << "Ya eres usuario premium!" << endl;
            return;
        }
        
        cout << "=== ACTUALIZAR A PREMIUM ===" << endl;
        cout << "Beneficios de Premium:" << endl;
        cout << "- Reproduccion ilimitada" << endl;
        cout << "- Audio en calidad 320 kbps" << endl;
        cout << "- Sin publicidad" << endl;
        cout << "- Lista de favoritos" << endl;
        cout << "- Precio: $19.900/mes" << endl;
        cout << "¿Deseas actualizar a Premium? (s/n): ";
        
        char respuesta;
        cin >> respuesta;
        cin.ignore();
        
        if (respuesta == 's' || respuesta == 'S') {
            // Crear nuevo usuario premium y transferir datos
            UsuarioPremium* nuevoPremium = new UsuarioPremium(
                usuarioActual->getNickname(), 
                "Ciudad Actual", 
                "Pais Actual", 
                "2024-01-01"
            );
            
            // Reemplazar el usuario en el vector
            for (auto& usuario : usuarios) {
                if (usuario == usuarioActual) {
                    delete usuario;
                    usuario = nuevoPremium;
                    usuarioActual = nuevoPremium;
                    break;
                }
            }
            
            cout << "¡Felicidades! Ahora eres usuario Premium." << endl;
        } else {
            cout << "Actualizacion cancelada." << endl;
        }
    }

public:
    void iniciarSesion() {
        string nickname;
        cout << "=== INICIAR SESION ===" << endl;
        cout << "Nickname: ";
        cin >> nickname;
        cin.ignore();
        
        for (auto usuario : usuarios) {
            if (usuario->getNickname() == nickname) {
                usuarioActual = usuario;
                cout << "¡Bienvenido " << nickname << "!" << endl;
                cout << "Tipo de membresia: " << usuario->getTipoMembresia() << endl;
                
                // Mostrar información de límite si es usuario estándar
                if (usuarioActual->getTipoMembresia() == "estandar") {
                    UsuarioEstandar* userEstandar = dynamic_cast<UsuarioEstandar*>(usuarioActual);
                    if (userEstandar) {
                        userEstandar->mostrarInfoLimite();
                    }
                }
                return;
            }
        }
        cout << "Usuario no encontrado" << endl;
    }
    
    void reproduccionAleatoria() {
        if (!usuarioActual) {
            cout << "Debe iniciar sesion primero" << endl;
            return;
        }
        
        // Verificar si puede reproducir (límite para estándar)
        if (!usuarioActual->puedeReproducir()) {
            cout << "¿Deseas actualizar a Premium para continuar? (s/n): ";
            char respuesta;
            cin >> respuesta;
            cin.ignore();
            
            if (respuesta == 's' || respuesta == 'S') {
                actualizarAPremium();
            }
            return;
        }
        
        int iteraciones = 0;
        cout << "=== REPRODUCCION ALEATORIA ===" << endl;
        
        // Obtener todas las canciones disponibles
        vector<Cancion*> todasLasCanciones;
        for (auto& artista : artistas) {
            for (auto& album : artista.getAlbumes()) {
                for (auto& cancion : album.getCanciones()) {
                    todasLasCanciones.push_back(&cancion);
                }
            }
        }
        
        if (todasLasCanciones.empty()) {
            cout << "No hay canciones disponibles" << endl;
            return;
        }
        
        srand(time(0));
        int cancionesReproducidas = 0;
        const int K = 5; // Limite de canciones para prueba
        
        reproduccionActiva = true;
        
        while (cancionesReproducidas < K && reproduccionActiva) {
            iteraciones++;
            
            // Seleccionar cancion aleatoria
            int indice = rand() % todasLasCanciones.size();
            Cancion* cancion = todasLasCanciones[indice];
            Album* albumPadre = nullptr;
            
            // Buscar el album padre (simplificado)
            for (auto& artista : artistas) {
                for (auto& album : artista.getAlbumes()) {
                    for (auto& can : album.getCanciones()) {
                        if (&can == cancion) {
                            albumPadre = &album;
                            break;
                        }
                    }
                    if (albumPadre) break;
                }
                if (albumPadre) break;
            }
            
            // Mostrar informacion de reproduccion
            cout << "\n--- REPRODUCIENDO ---" << endl;
            cout << "Artista: Artista " << (cancion->getId() / 10000) << endl;
            cout << "Album: " << (albumPadre ? albumPadre->getNombre() : "Desconocido") << endl;
            cout << "Ruta a la portada: " << (albumPadre ? albumPadre->getRutaPortada() : "N/A") << endl;
            cout << "Cancion: " << cancion->getNombre() << endl;
            
            if (usuarioActual->getTipoMembresia() == "premium") {
                cout << "Ruta al audio: " << cancion->getRuta320() << " (320 kbps)" << endl;
            } else {
                cout << "Ruta al audio: " << cancion->getRuta128() << " (128 kbps)" << endl;
            }
            
            cout << "Duracion: " << cancion->getDuracion() << " segundos" << endl;
            
            // Mostrar publicidad para usuarios estandar
            if (usuarioActual->getTipoMembresia() == "estandar") {
                UsuarioEstandar* userEstandar = dynamic_cast<UsuarioEstandar*>(usuarioActual);
                if (userEstandar && userEstandar->debeMostrarPublicidad()) {
                    int indiceMensaje = rand() % mensajesPublicitarios.size();
                    cout << "\n[MENSAJE PUBLICITARIO]" << endl;
                    cout << mensajesPublicitarios[indiceMensaje].getContenido() << endl;
                    cout << "Categoria: " << mensajesPublicitarios[indiceMensaje].getCategoria() << endl;
                }
            }
            
            // Mostrar opciones de reproduccion con opción de detener
            cout << "\nOpciones de reproduccion:" << endl;
            cout << "1. Reproducir  2. Detener";
            if (usuarioActual->getTipoMembresia() == "premium") {
                cout << "  3. Siguiente  4. Anterior  5. Repetir";
            }
            cout << endl;
            cout << "Seleccione opcion (1 para continuar, 2 para detener): ";
            
            int opcionReproduccion;
            cin >> opcionReproduccion;
            cin.ignore();
            
            if (opcionReproduccion == 2) {
                cout << "Reproduccion detenida por el usuario." << endl;
                reproduccionActiva = false;
                break;
            }
            
            cancion->reproducir();
            usuarioActual->incrementarReproducciones(); // Incrementar contador
            cancionesReproducidas++;
            
            // Mostrar información de límite para usuarios estándar
            if (usuarioActual->getTipoMembresia() == "estandar") {
                UsuarioEstandar* userEstandar = dynamic_cast<UsuarioEstandar*>(usuarioActual);
                if (userEstandar) {
                    userEstandar->mostrarInfoLimite();
                }
            }
            
            // Temporizador de 3 segundos (simplificado)
            cout << "Reproduciendo... (3 segundos)" << endl;
            
            // Simular reproducción con posibilidad de interrupción
            for (int i = 0; i < 3 && reproduccionActiva; i++) {
                this_thread::sleep_for(chrono::seconds(1));
                // Podríamos agregar aquí una verificación de entrada del usuario
                // para pausar/detener durante la reproducción
            }
            
            if (reproduccionActiva) {
                cout << "Pasando a la siguiente cancion..." << endl;
            }
        }
        
        // Actualizar metricas
        usuarioActual->setIteraciones(iteraciones);
        calcularMemoriaTotal();
        usuarioActual->setMemoria(totalMemoria);
        
        if (reproduccionActiva) {
            cout << "\nReproduccion finalizada (" << cancionesReproducidas << " canciones reproducidas)" << endl;
        }
        usuarioActual->mostrarMetricas();
        reproduccionActiva = false;
    }
    void menuFavoritos() {
        if (!usuarioActual || usuarioActual->getTipoMembresia() != "premium") {
            cout << "Esta funcionalidad es solo para usuarios premium" << endl;
            return;
        }
        
        UsuarioPremium* userPremium = dynamic_cast<UsuarioPremium*>(usuarioActual);
        if (!userPremium) return;
        
        int opcion;
        do {
            cout << "\n=== MI LISTA DE FAVORITOS ===" << endl;
            cout << "1. Editar mi lista de favoritos" << endl;
            cout << "2. Seguir otra lista de favoritos" << endl;
            cout << "3. Ejecutar mi lista de favoritos" << endl;
            cout << "4. Mostrar mis favoritos" << endl;
            cout << "0. Volver al menu principal" << endl;
            cout << "Opcion: ";
            cin >> opcion;
            cin.ignore();
            
            switch (opcion) {
                case 1: {
                    cout << "\n--- EDITAR FAVORITOS ---" << endl;
                    cout << "1. Agregar cancion  2. Eliminar cancion: ";
                    int subopcion;
                    cin >> subopcion;
                    cin.ignore();
                    
                    if (subopcion == 1) {
                        // Buscar canciones disponibles
                        cout << "Canciones disponibles:" << endl;
                        int contador = 1;
                        vector<Cancion*> disponibles;
                        
                        for (auto& artista : artistas) {
                            for (auto& album : artista.getAlbumes()) {
                                for (auto& cancion : album.getCanciones()) {
                                    cout << contador << ". " << cancion.getNombre() 
                                         << " (ID: " << cancion.getId() << ")" << endl;
                                    disponibles.push_back(&cancion);
                                    contador++;
                                }
                            }
                        }
                        
                        if (disponibles.empty()) {
                            cout << "No hay canciones disponibles" << endl;
                            break;
                        }
                        
                        cout << "Seleccione el numero de la cancion: ";
                        int seleccion;
                        cin >> seleccion;
                        cin.ignore();
                        
                        if (seleccion >= 1 && seleccion <= disponibles.size()) {
                            userPremium->agregarFavorito(*disponibles[seleccion-1]);
                        } else {
                            cout << "Seleccion invalida" << endl;
                        }
                        
                    } else if (subopcion == 2) {
                        cout << "ID de la cancion a eliminar: ";
                        int id;
                        cin >> id;
                        cin.ignore();
                        userPremium->eliminarFavorito(id);
                    }
                    break;
                }
                case 2: {
                    cout << "\n--- SEGUIR USUARIO ---" << endl;
                    cout << "Nickname del usuario a seguir: ";
                    string nickname;
                    cin >> nickname;
                    cin.ignore();
                    userPremium->seguirUsuario(nickname);
                    break;
                }
                case 3: {
                    cout << "\n--- EJECUTAR FAVORITOS ---" << endl;
                    vector<Cancion>& favoritos = userPremium->getFavoritos();
                    
                    if (favoritos.empty()) {
                        cout << "No hay canciones en favoritos" << endl;
                        break;
                    }
                    
                    userPremium->mostrarFavoritos();
                    cout << "¿Reproducir en orden original (1) o aleatorio (2)? ";
                    int orden;
                    cin >> orden;
                    cin.ignore();
                    
                    // Simular reproduccion de favoritos
                    cout << "Reproduciendo lista de favoritos..." << endl;
                    for (size_t i = 0; i < favoritos.size() && i < 3; i++) { // Limitar a 3 para demo
                        cout << "Reproduciendo: " << favoritos[i].getNombre() << endl;
                        this_thread::sleep_for(chrono::seconds(2));
                    }
                    cout << "Reproduccion de favoritos completada" << endl;
                    break;
                }
                case 4:
                    userPremium->mostrarFavoritos();
                    break;
                case 0:
                    cout << "Volviendo al menu principal..." << endl;
                    break;
                default:
                    cout << "Opcion invalida" << endl;
            }
        } while (opcion != 0);
    }
    
    void mostrarMenuPrincipal() {
        int opcion;
        do {
            cout << "\n=== UDEATUNES ===" << endl;
            if (usuarioActual) {
                cout << "Usuario: " << usuarioActual->getNickname();
                cout << " (" << usuarioActual->getTipoMembresia() << ")" << endl;
            }
            
            // MOSTRAR OPCIONES DEPENDIENDO DE SI HAY SESIÓN ACTIVA O NO
            if (!usuarioActual) {
                // Si NO hay sesión activa
                cout << "1. Iniciar sesion" << endl;
                cout << "2. Reproduccion aleatoria (requiere sesion)" << endl;
            } else {
                // Si HAY sesión activa - QUITAMOS "Iniciar sesion"
                cout << "2. Reproduccion aleatoria" << endl;
                if (usuarioActual->getTipoMembresia() == "premium") {
                    cout << "3. Mi lista de favoritos" << endl;
                }
                cout << "4. Cerrar sesion" << endl;
                cout << "5. Actualizar a Premium" << endl;
            }
            
            cout << "0. Salir" << endl;
            cout << "Opcion: ";
            cin >> opcion;
            cin.ignore();
            
            switch (opcion) {
                case 1:
                    if (!usuarioActual) {
                        iniciarSesion();
                    } else {
                        cout << "Opcion no disponible" << endl;
                    }
                    break;
                case 2:
                    reproduccionAleatoria();
                    break;
                case 3:
                    if (usuarioActual && usuarioActual->getTipoMembresia() == "premium") {
                        menuFavoritos();
                    } else {
                        cout << "Opcion no disponible" << endl;
                    }
                    break;
                case 4:
                    if (usuarioActual) {
                        cerrarSesion();
                    } else {
                        cout << "Opcion no disponible" << endl;
                    }
                    break;
                case 5:
                    if (usuarioActual) {
                        actualizarAPremium();
                    } else {
                        cout << "Opcion no disponible" << endl;
                    }
                    break;
                case 0:
                    cout << "¡Hasta pronto!" << endl;
                    break;
                default:
                    cout << "Opcion invalida" << endl;
            }
        } while (opcion != 0);
    }
};

// ============================ FUNCION MAIN ============================

int main() {
    cout << "Bienvenido a UdeATunes - Sistema de Streaming Musical" << endl;
    
    UdeATunes sistema;
    sistema.mostrarMenuPrincipal();
    
    return 0;
}

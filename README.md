#include <iostream>
#include <string>
#include <vector>
#include <cstdlib>
#include <ctime>
#include <chrono>
#include <thread>
#include <limits>

using namespace std;

// ============================ CLASES PRINCIPALES ============================

class Credito {
private:
    string nombre;
    string apellido;
    string codigoAfiliacion;
    string categoria;

public:
    Credito(string n, string a, string c, string cat)
        : nombre(n), apellido(a), codigoAfiliacion(c), categoria(cat) {}

    string getNombre() const { return nombre; }
    string getApellido() const { return apellido; }
    string getCodigo() const { return codigoAfiliacion; }
    string getCategoria() const { return categoria; }
};

class Cancion {
private:
    string nombre;
    int identificador;
    int duracion;
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

    string getNombre() const { return nombre; }
    int getId() const { return identificador; }
    int getDuracion() const { return duracion; }
    string getRuta128() const { return rutaAudio128; }
    string getRuta320() const { return rutaAudio320; }
    int getReproducciones() const { return reproducciones; }

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

    string getNombre() const { return nombre; }
    int getId() const { return identificador; }
    string getRutaPortada() const { return rutaPortada; }
    vector<Cancion>& getCanciones() { return canciones; }
    const vector<Cancion>& getCanciones() const { return canciones; }

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
    string pais;

public:
    vector<Album> albumes;

    Artista(int c, string p)
        : codigo(c), pais(p) {}

    void agregarAlbum(const Album& album) {
        albumes.push_back(album);
    }

    int getCodigo() const { return codigo; }
    string getPais() const { return pais; }
    vector<Album>& getAlbumes() { return albumes; }
    const vector<Album>& getAlbumes() const { return albumes; }
};

class MensajePublicitario {
private:
    string contenido;
    string categoria;
    int prioridad;

public:
    MensajePublicitario(string cont, string cat)
        : contenido(cont), categoria(cat) {
        if (categoria == "C") prioridad = 1;
        else if (categoria == "B") prioridad = 2;
        else if (categoria == "AAA") prioridad = 3;
        else prioridad = 1;
    }

    string getContenido() const { return contenido; }
    string getCategoria() const { return categoria; }
    int getPrioridad() const { return prioridad; }
};

class Usuario {
protected:
    string nickname;
    string tipoMembresia;
    string ciudad;
    string pais;
    string fechaInscripcion;
    int iteracionesConsumidas;
    int memoriaConsumida;
    int reproduccionesRealizadas;

public:
    Usuario(string nick, string tipo, string ciu, string pa, string fecha)
        : nickname(nick), tipoMembresia(tipo), ciudad(ciu),
        pais(pa), fechaInscripcion(fecha), iteracionesConsumidas(0),
        memoriaConsumida(0), reproduccionesRealizadas(0) {}

    virtual ~Usuario() {}

    string getNickname() const { return nickname; }
    string getTipoMembresia() const { return tipoMembresia; }
    string getCiudad() const { return ciudad; }
    string getPais() const { return pais; }
    string getFechaInscripcion() const { return fechaInscripcion; }
    int getIteraciones() const { return iteracionesConsumidas; }
    int getMemoria() const { return memoriaConsumida; }
    int getReproduccionesRealizadas() const { return reproduccionesRealizadas; }

    void setIteraciones(int iter) { iteracionesConsumidas = iter; }
    void setMemoria(int mem) { memoriaConsumida = mem; }
    void incrementarReproducciones() { reproduccionesRealizadas++; }

    virtual void mostrarMetricas() {
        cout << "=== METRICAS DE CONSUMO ===" << endl;
        cout << "Iteraciones requeridas: " << iteracionesConsumidas << endl;
        cout << "Memoria consumida: " << memoriaConsumida << " bytes" << endl;
        cout << "Reproducciones realizadas: " << reproduccionesRealizadas << endl;
        cout << "============================" << endl;
    }

    virtual bool puedeReproducir() {
        return true;
    }

    virtual void cambiarMembresia(const string& nuevaMembresia) {
        tipoMembresia = nuevaMembresia;
        cout << "Membresia cambiada a: " << tipoMembresia << endl;
    }
};

class UsuarioEstandar : public Usuario {
private:
    int cancionesReproducidas;
    static const int LIMITE_REPRODUCCIONES = 5;

public:
    UsuarioEstandar(string nick, string ciu, string pa, string fecha)
        : Usuario(nick, "estandar", ciu, pa, fecha), cancionesReproducidas(0) {}

    bool debeMostrarPublicidad() {
        cancionesReproducidas++;
        return (cancionesReproducidas % 2 == 0);
    }

    bool puedeReproducir() override {
        if (getReproduccionesRealizadas() >= LIMITE_REPRODUCCIONES) {
            cout << "¡Has alcanzado el limite de " << LIMITE_REPRODUCCIONES << " reproducciones!" << endl;
            cout << "Actualiza a Premium para reproduccion ilimitada." << endl;
            return false;
        }
        return true;
    }

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
        if (favoritos.empty()) {
            cout << "No hay canciones en favoritos" << endl;
            return;
        }
        for (size_t i = 0; i < favoritos.size(); i++) {
            cout << (static_cast<int>(i)+1) << ". " << favoritos[i].getNombre()
            << " (ID: " << favoritos[i].getId() << ")" << endl;
        }
        cout << "Total: " << favoritos.size() << " canciones" << endl;
    }

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
    bool reproduccionActiva;

    // Función para limpiar el buffer de entrada
    void limpiarBuffer() {
        cin.clear();
        cin.ignore(numeric_limits<streamsize>::max(), '\n');
    }

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
        mensajesPublicitarios.push_back(MensajePublicitario("¡Descuento del 20% en audifonos!", "B"));
        mensajesPublicitarios.push_back(MensajePublicitario("Suscribete a Premium por solo $19.900!", "AAA"));
        mensajesPublicitarios.push_back(MensajePublicitario("Nuevo album disponible esta semana", "C"));

        // Crear artistas y albumes de ejemplo
        Artista artista1(12345, "Colombia");

        Album album1("Lugar Secreto", 12, "2024-03-01", "Sello Indie",
                     "/portadas/lugar_secreto.png", 8.5);
        album1.agregarGenero("Pop");
        album1.agregarGenero("Rock");

        // Agregar canciones al álbum 1
        Cancion cancion1("A tu lado", 123451201, 240,
                         "/audio/a_tu_lado_128.ogg",
                         "/audio/a_tu_lado_320.ogg");

        Cancion cancion2("Noche estrellada", 123451202, 210,
                         "/audio/noche_estrellada_128.ogg",
                         "/audio/noche_estrellada_320.ogg");

        Cancion cancion3("Camino al amanecer", 123451203, 195,
                         "/audio/camino_amanecer_128.ogg",
                         "/audio/camino_amanecer_320.ogg");

        Credito credito1("Carlos", "Gomez", "A123456789", "Compositor");
        cancion1.agregarCredito(credito1);
        cancion2.agregarCredito(credito1);
        cancion3.agregarCredito(credito1);

        album1.agregarCancion(cancion1);
        album1.agregarCancion(cancion2);
        album1.agregarCancion(cancion3);

        // Crear un segundo álbum
        Album album2("Vida en Color", 13, "2024-04-15", "Sello Musical",
                     "/portadas/vida_en_color.png", 9.0);
        album2.agregarGenero("Pop");
        album2.agregarGenero("Electronica");

        Cancion cancion4("Bailando con la luna", 123451301, 220,
                         "/audio/bailando_luna_128.ogg",
                         "/audio/bailando_luna_320.ogg");

        Cancion cancion5("Ritmo urbano", 123451302, 245,
                         "/audio/ritmo_urbano_128.ogg",
                         "/audio/ritmo_urbano_320.ogg");

        cancion4.agregarCredito(credito1);
        cancion5.agregarCredito(credito1);

        album2.agregarCancion(cancion4);
        album2.agregarCancion(cancion5);

        artista1.agregarAlbum(album1);
        artista1.agregarAlbum(album2);
        artistas.push_back(artista1);

        calcularMemoriaTotal();
    }

    void calcularMemoriaTotal() {
        totalMemoria = 0;

        for (const auto& usuario : usuarios) {
            totalMemoria += sizeof(*usuario);
        }

        for (const auto& artista : artistas) {
            totalMemoria += sizeof(artista);
            for (const auto& album : artista.getAlbumes()) {
                totalMemoria += sizeof(album);
                for (const auto& cancion : album.getCanciones()) {
                    totalMemoria += sizeof(cancion);
                }
            }
        }

        for (const auto& mensaje : mensajesPublicitarios) {
            totalMemoria += sizeof(mensaje);
        }
    }

    vector<Cancion*> obtenerTodasLasCanciones() {
        vector<Cancion*> todasLasCanciones;
        for (auto& artista : artistas) {
            for (auto& album : artista.getAlbumes()) {
                for (auto& cancion : album.getCanciones()) {
                    todasLasCanciones.push_back(&cancion);
                }
            }
        }
        return todasLasCanciones;
    }

    Album* buscarAlbumDeCancion(const Cancion* cancionBuscada) {
        for (auto& artista : artistas) {
            for (auto& album : artista.getAlbumes()) {
                for (auto& cancion : album.getCanciones()) {
                    if (&cancion == cancionBuscada) {
                        return &album;
                    }
                }
            }
        }
        return nullptr;
    }

    Artista* buscarArtistaDeAlbum(const Album* albumBuscado) {
        for (auto& artista : artistas) {
            for (auto& album : artista.getAlbumes()) {
                if (&album == albumBuscado) {
                    return &artista;
                }
            }
        }
        return nullptr;
    }

    void cerrarSesion() {
        if (usuarioActual) {
            cout << "Cerrando sesion de " << usuarioActual->getNickname() << "..." << endl;
            usuarioActual = nullptr;
            reproduccionActiva = false;
        } else {
            cout << "No hay ninguna sesion activa" << endl;
        }
    }

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
        limpiarBuffer();

        if (respuesta == 's' || respuesta == 'S') {
            // Crear nuevo usuario premium con la información del usuario actual
            UsuarioPremium* nuevoPremium = new UsuarioPremium(
                usuarioActual->getNickname(),
                usuarioActual->getCiudad(),
                usuarioActual->getPais(),
                usuarioActual->getFechaInscripcion()
                );

            // Transferir métricas existentes
            nuevoPremium->setIteraciones(usuarioActual->getIteraciones());
            nuevoPremium->setMemoria(usuarioActual->getMemoria());

            // Reemplazar el usuario en el vector
            for (size_t i = 0; i < usuarios.size(); i++) {
                if (usuarios[i] == usuarioActual) {
                    delete usuarios[i];
                    usuarios[i] = nuevoPremium;
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
        getline(cin, nickname);

        for (auto usuario : usuarios) {
            if (usuario->getNickname() == nickname) {
                usuarioActual = usuario;
                cout << "¡Bienvenido " << nickname << "!" << endl;
                cout << "Tipo de membresia: " << usuario->getTipoMembresia() << endl;

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

        if (!usuarioActual->puedeReproducir()) {
            cout << "¿Deseas actualizar a Premium para continuar? (s/n): ";
            char respuesta;
            cin >> respuesta;
            limpiarBuffer();

            if (respuesta == 's' || respuesta == 'S') {
                actualizarAPremium();
            }
            return;
        }

        int iteraciones = 0;
        cout << "=== REPRODUCCION ALEATORIA ===" << endl;

        vector<Cancion*> todasLasCanciones = obtenerTodasLasCanciones();

        if (todasLasCanciones.empty()) {
            cout << "No hay canciones disponibles" << endl;
            return;
        }

        srand(time(0));
        int cancionesReproducidas = 0;
        const int K = 3;

        reproduccionActiva = true;

        while (cancionesReproducidas < K && reproduccionActiva && !todasLasCanciones.empty()) {
            iteraciones++;

            int indice = rand() % todasLasCanciones.size();
            Cancion* cancionSeleccionada = todasLasCanciones[indice];

            Album* albumPadre = buscarAlbumDeCancion(cancionSeleccionada);
            Artista* artistaPadre = albumPadre ? buscarArtistaDeAlbum(albumPadre) : nullptr;

            cout << "\n--- REPRODUCIENDO ---" << endl;
            if (artistaPadre) {
                cout << "Artista: Codigo " << artistaPadre->getCodigo() << endl;
            } else {
                cout << "Artista: Desconocido" << endl;
            }
            cout << "Album: " << (albumPadre ? albumPadre->getNombre() : "Desconocido") << endl;
            cout << "Cancion: " << cancionSeleccionada->getNombre() << endl;
            cout << "ID Cancion: " << cancionSeleccionada->getId() << endl;

            if (usuarioActual->getTipoMembresia() == "premium") {
                cout << "Calidad: 320 kbps" << endl;
            } else {
                cout << "Calidad: 128 kbps" << endl;
            }

            cout << "Duracion: " << cancionSeleccionada->getDuracion() << " segundos" << endl;

            if (usuarioActual->getTipoMembresia() == "estandar") {
                UsuarioEstandar* userEstandar = dynamic_cast<UsuarioEstandar*>(usuarioActual);
                if (userEstandar && userEstandar->debeMostrarPublicidad()) {
                    int indiceMensaje = rand() % mensajesPublicitarios.size();
                    cout << "\n[MENSAJE PUBLICITARIO]" << endl;
                    cout << mensajesPublicitarios[indiceMensaje].getContenido() << endl;
                    cout << "Categoria: " << mensajesPublicitarios[indiceMensaje].getCategoria() << endl;
                }
            }

            cout << "\nOpciones:" << endl;
            cout << "1. Continuar  2. Detener: ";

            int opcionReproduccion;
            if (!(cin >> opcionReproduccion)) {
                limpiarBuffer();
                cout << "Entrada invalida. Deteniendo reproduccion..." << endl;
                reproduccionActiva = false;
                break;
            }
            limpiarBuffer();

            if (opcionReproduccion == 2) {
                cout << "Reproduccion detenida por el usuario." << endl;
                reproduccionActiva = false;
                break;
            }

            cancionSeleccionada->reproducir();
            usuarioActual->incrementarReproducciones();
            cancionesReproducidas++;

            if (usuarioActual->getTipoMembresia() == "estandar") {
                UsuarioEstandar* userEstandar = dynamic_cast<UsuarioEstandar*>(usuarioActual);
                if (userEstandar) {
                    userEstandar->mostrarInfoLimite();
                }
            }

            cout << "Reproduciendo... (2 segundos)" << endl;
            this_thread::sleep_for(chrono::seconds(2));

            if (reproduccionActiva) {
                cout << "Pasando a la siguiente cancion..." << endl;
            }
        }

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
        if (!usuarioActual) {
            cout << "Debe iniciar sesion primero" << endl;
            return;
        }

        if (usuarioActual->getTipoMembresia() != "premium") {
            cout << "Esta funcionalidad es solo para usuarios premium" << endl;
            return;
        }

        UsuarioPremium* userPremium = dynamic_cast<UsuarioPremium*>(usuarioActual);
        if (!userPremium) {
            cout << "Error: No se pudo acceder a las funcionalidades premium" << endl;
            return;
        }

        int opcion;
        do {
            cout << "\n=== MI LISTA DE FAVORITOS ===" << endl;
            cout << "1. Mostrar mis favoritos" << endl;
            cout << "2. Agregar cancion a favoritos" << endl;
            cout << "3. Eliminar cancion de favoritos" << endl;
            cout << "4. Seguir usuario" << endl;
            cout << "5. Reproducir favoritos" << endl;
            cout << "0. Volver al menu principal" << endl;
            cout << "Opcion: ";

            if (!(cin >> opcion)) {
                limpiarBuffer();
                cout << "Entrada invalida. Intente nuevamente." << endl;
                continue;
            }
            limpiarBuffer();

            switch (opcion) {
            case 1:
                userPremium->mostrarFavoritos();
                break;
            case 2: {
                vector<Cancion*> disponibles = obtenerTodasLasCanciones();
                cout << "Canciones disponibles:" << endl;
                for (size_t i = 0; i < disponibles.size(); i++) {
                    cout << (static_cast<int>(i)+1) << ". " << disponibles[i]->getNombre()
                    << " (ID: " << disponibles[i]->getId() << ")" << endl;
                }
                if (!disponibles.empty()) {
                    cout << "Seleccione el numero de la cancion: ";
                    int seleccion;
                    if (!(cin >> seleccion)) {
                        limpiarBuffer();
                        cout << "Entrada invalida." << endl;
                        break;
                    }
                    limpiarBuffer();
                    if (seleccion >= 1 && seleccion <= static_cast<int>(disponibles.size())) {
                        userPremium->agregarFavorito(*disponibles[seleccion-1]);
                    } else {
                        cout << "Seleccion invalida" << endl;
                    }
                } else {
                    cout << "No hay canciones disponibles" << endl;
                }
                break;
            }
            case 3: {
                cout << "ID de la cancion a eliminar: ";
                int id;
                if (!(cin >> id)) {
                    limpiarBuffer();
                    cout << "ID invalido." << endl;
                    break;
                }
                limpiarBuffer();
                userPremium->eliminarFavorito(id);
                break;
            }
            case 4: {
                cout << "Nickname del usuario a seguir: ";
                string nickname;
                getline(cin, nickname);
                userPremium->seguirUsuario(nickname);
                break;
            }
            case 5: {
                vector<Cancion>& favoritos = userPremium->getFavoritos();
                if (favoritos.empty()) {
                    cout << "No hay canciones en favoritos" << endl;
                    break;
                }
                cout << "Reproduciendo lista de favoritos..." << endl;
                for (size_t i = 0; i < favoritos.size() && i < 2; i++) {
                    cout << "Reproduciendo: " << favoritos[i].getNombre() << endl;
                    this_thread::sleep_for(chrono::seconds(2));
                }
                cout << "Reproduccion de favoritos completada" << endl;
                break;
            }
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
            } else {
                cout << "No hay usuario activo" << endl;
            }

            cout << "\nMenu Principal:" << endl;
            if (!usuarioActual) {
                cout << "1. Iniciar sesion" << endl;
                cout << "2. Salir" << endl;
            } else {
                cout << "1. Reproduccion aleatoria" << endl;
                if (usuarioActual->getTipoMembresia() == "premium") {
                    cout << "2. Mi lista de favoritos" << endl;
                    cout << "3. Cerrar sesion" << endl;
                    cout << "4. Salir" << endl;
                } else {
                    cout << "2. Actualizar a Premium" << endl;
                    cout << "3. Cerrar sesion" << endl;
                    cout << "4. Salir" << endl;
                }
            }

            cout << "\nSeleccione una opcion: ";

            if (!(cin >> opcion)) {
                limpiarBuffer();
                cout << "Entrada invalida. Por favor, ingrese un numero." << endl;
                continue;
            }
            limpiarBuffer();

            if (!usuarioActual) {
                // Menu sin usuario
                switch (opcion) {
                case 1:
                    iniciarSesion();
                    break;
                case 2:
                    cout << "¡Hasta pronto!" << endl;
                    return;
                default:
                    cout << "Opcion invalida. Por favor, seleccione 1 o 2." << endl;
                }
            } else {
                // Menu con usuario
                switch (opcion) {
                case 1:
                    reproduccionAleatoria();
                    break;
                case 2:
                    if (usuarioActual->getTipoMembresia() == "premium") {
                        menuFavoritos();
                    } else {
                        actualizarAPremium();
                    }
                    break;
                case 3:
                    cerrarSesion();
                    break;
                case 4:
                    cout << "¡Hasta pronto!" << endl;
                    return;
                default:
                    cout << "Opcion invalida. Por favor, seleccione una opcion valida." << endl;
                }
            }
        } while (true);
    }
};

// ============================ FUNCION MAIN ============================

int main() {
    cout << "Bienvenido a UdeATunes - Sistema de Streaming Musical" << endl;
    cout << "=====================================================" << endl;

    UdeATunes sistema;
    sistema.mostrarMenuPrincipal();

    return 0;
}

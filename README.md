#include <iostream>
#include <string>
using namespace std;

class Usuario {
private:
    string nickname;
    string ciudad;
    string pais;
    string fechaInscripcion;
    bool esPremium;
public:
    Usuario() {}
    Usuario(const Usuario&) {}
    ~Usuario() {}
    string getNickname() const { return nickname; }
    bool getEsPremium() const { return esPremium; }
    void setEsPremium(bool value) { esPremium = value; }
    void mostrarInfo() const {
        cout << "Usuario: " << nickname << endl;
    }
};

class Cancion {
private:
    int identificador;
    string nombre;
    int duracion;
public:
    Cancion() {}
    Cancion(const Cancion&) {}
    ~Cancion() {}
    int getIdentificador() const { return identificador; }
    string getNombre() const { return nombre; }
    void incrementarReproducciones() {}
    void mostrarInfo() const {
        cout << "Cancion: " << nombre << endl;
    }
};

class Plataforma {
private:
    Usuario** usuarios;
    int numUsuarios;
    Cancion** todasCanciones;
    int numCanciones;
public:
    Plataforma() : usuarios(nullptr), numUsuarios(0), todasCanciones(nullptr), numCanciones(0) {}
    ~Plataforma() {}
    bool iniciarSesion(string nickname) { return false; }
    void reproduccionAleatoria() {}
    Usuario* buscarUsuario(string) const { return nullptr; }
    Cancion* buscarCancion(int) const { return nullptr; }
};

int main() {
    cout << "Probando plataforma..." << endl;
    Plataforma p;
    return 0;
}

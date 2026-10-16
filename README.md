# FirmaEC para Arch Linux y CachyOS (`firmaec-bin`)

Instalador y paquete no oficial para ejecutar **FirmaEC** de forma nativa en distribuciones basadas en Arch Linux (CachyOS, Manjaro, EndeavourOS, Arch Linux).

Este empaquetado resuelve la incompatibilidad de formatos al convertir e integrar la versión oficial distribuida para Debian/Ubuntu mediante el gestor nativo `pacman`, configurando automáticamente el entorno de ejecución Java, los binarios del sistema y el acceso directo de escritorio con su icono oficial.

---

## Características

- **Gestión nativa con pacman:** instalación, actualización y desinstalación limpia sin dejar archivos huérfanos en el sistema.
- **Acceso directo corregido:** integra el ejecutable gráfico de escritorio (`/usr/bin/firmador`) directamente en el menú de aplicaciones, evitando el error de protocolo web (`Error: Se debe enviar un parámetro`).
- **Descarga oficial y estable:** obtiene los componentes verificados desde el mirror oficial de CEDIA Ecuador.
- **Compatibilidad con tokens USB:** soporte opcional para lectores de tarjetas inteligentes y tokens criptográficos mediante `pcscd`.

---

## Requisitos previos

Asegúrate de contar con las herramientas base de desarrollo y descarga instaladas:

```bash
sudo pacman -S --needed base-devel git
```

---

## Instalación rápida (vía Git)

Ejecuta los siguientes comandos en tu terminal:

```bash
# 1. Clonar este repositorio
git clone https://github.com/Jhostin-Gonzalez/firmaec-bin.git

# 2. Entrar a la carpeta del proyecto
cd firmaec-bin

# 3. Compilar e instalar el paquete nativo y sus dependencias
makepkg -si
```

Durante el proceso, el sistema resolverá automáticamente la dependencia del entorno de ejecución Java (`jre17-openjdk`) y compilará el paquete `.pkg.tar.zst` para instalarlo en el sistema operativo.

---

## Uso

Una vez instalado, puedes iniciar la aplicación de dos formas:

1. **Desde el menú de aplicaciones:** presiona la tecla `Super` / `Windows`, busca **FirmaEC** y haz clic sobre el lanzador.
2. **Desde la terminal:**

   ```bash
   firmador
   ```

> **Nota:** el comando `firmaec` se mantiene configurado en el sistema para atender peticiones invocadas desde navegadores web (plataformas estatales como Quipux o portales ministeriales). Para el uso diario de escritorio se debe utilizar el comando `firmador`.

---

## Estructura del archivo PKGBUILD

Para inspección o auditoría del empaquetado, este repositorio utiliza el siguiente esquema `PKGBUILD`:

```bash
# Maintainer: Jhostin Gonzalez <jhostings2@proton.me>
pkgname=firmaec-bin
pkgver=5.3.0
pkgrel=1
pkgdesc="Aplicacion oficial del Gobierno de Ecuador para firma y validacion electronica de documentos"
arch=('x86_64')
url="https://www.firmadigital.gob.ec"
license=('custom')
depends=('jre17-openjdk' 'gtk3' 'shared-mime-info')
optdepends=(
    'ccid: Soporte para lectores de tarjetas inteligentes y tokens USB'
    'pcsc-tools: Herramientas de diagnostico de tarjetas y tokens'
)
provides=('firmaec' 'firmador')
conflicts=('firmaec')

source=("https://mirror.cedia.org.ec/firmaec/firmaec_amd64.deb")
sha256sums=('SKIP')

package() {
    # Extraer el contenedor de datos del paquete Debian
    bsdtar -xf "${srcdir}/data.tar.xz" -C "${pkgdir}/" 2>/dev/null || \
    bsdtar -xf "${srcdir}/data.tar.zst" -C "${pkgdir}/" 2>/dev/null || \
    bsdtar -xf "${srcdir}/data.tar.gz" -C "${pkgdir}/"

    # Permisos de ejecucion en binarios del sistema
    chmod 755 "${pkgdir}/usr/bin/firmaec"
    chmod 755 "${pkgdir}/usr/bin/firmador"

    # Acceso directo para escritorio
    install -d "${pkgdir}/usr/share/applications"
    cat << 'EOF' > "${pkgdir}/usr/share/applications/firmaec.desktop"
[Desktop Entry]
Name=FirmaEC
Comment=Firma electronica de documentos
Exec=/usr/bin/firmador
Icon=/usr/share/firmaec/firmaec.png
Terminal=false
Type=Application
Categories=Office;Utility;
EOF
}
```

---

## Configuración opcional para token USB físico

Si firmas mediante dispositivo USB criptográfico en lugar de archivo digital (`.p12` / `.pfx`), habilita el daemon de lectura de tarjetas inteligentes:

```bash
# Instalar los controladores y herramientas de lectura
sudo pacman -S ccid pcsc-tools

# Activar e iniciar el servicio del sistema
sudo systemctl enable --now pcscd.service
```

---

## Desinstalación

Para eliminar FirmaEC y todos sus archivos del sistema sin dejar residuos:

```bash
sudo pacman -R firmaec-bin
```
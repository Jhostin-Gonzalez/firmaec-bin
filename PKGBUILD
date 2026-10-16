# Maintainer: Jhostin Gonzalez <jhostings2@proton.me>
pkgname=firmaec-bin
pkgver=5.3.0
pkgrel=1
pkgdesc="Aplicación oficial del Gobierno de Ecuador para firma y validación electrónica de documentos"
arch=('x86_64')
url="https://www.firmadigital.gob.ec"
license=('custom')
depends=('jre17-openjdk' 'gtk3' 'shared-mime-info')
optdepends=(
    'ccid: Soporte para lectores de tarjetas inteligentes y tokens USB'
    'pcsc-tools: Herramientas de diagnóstico de tarjetas y tokens'
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

    # Asegurar permisos de ejecución en los binarios
    chmod 755 "${pkgdir}/usr/bin/firmaec"
    chmod 755 "${pkgdir}/usr/bin/firmador"

    # Crear el acceso directo de escritorio para abrir directamente la interfaz gráfica
    install -d "${pkgdir}/usr/share/applications"
    cat << 'EOF' > "${pkgdir}/usr/share/applications/firmaec.desktop"
[Desktop Entry]
Name=FirmaEC
Comment=Firma electrónica de documentos
Exec=/usr/bin/firmador
Icon=/usr/share/firmaec/firmaec.png
Terminal=false
Type=Application
Categories=Office;Utility;
EOF
}
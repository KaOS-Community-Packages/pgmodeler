pkgname=pgmodeler
pkgver=0.9.2_beta
pkgrel=1
pkgdesc="PostgreSQL Database Modeler: an open source CASE tool for modeling PostgreSQL databases"

url="https://pgmodeler.io/"
license=('GPL3')

arch=('x86_64')

depends=('qt5-base' 'qt5-svg' 'libxml2' 'libsm' 'postgresql-libs')
makedepends=('qt5-tools' 'postgresql-libs')
optdepends=('postgresql')

source=("https://github.com/$pkgname/$pkgname/archive/v${pkgver//_/-}.tar.gz"
        'pgmodeler'
        'pgmodeler-cli'
        'pgmodeler_logo.png'
        'pgmodeler_dbm.png'
        'pgmodeler.desktop'
    'patch_no_check_update.diff')
sha1sums=('3355bebc838984e2700289459416edd8d0c5c5b8'
          'ffe14e0cdd61392303b7edd8fe7a72047d7cb1c7'
          '30d3016d227f1d8d043d3966ec705c04318b93f4'
          'c5bb090a1cbb784cd2ec9e1449cac02af2ba6538'
          '4c4e4260f4b2d2d4c154a8fb5cd7060a6585c83a'
          '432c320d7ca3474e7c735e7e4116c01dedf66f37'
          '448e756999f5770680eb039f590dd61eb6225b34')

install='pgmodeler.install'
PGMODELER_ROOT=/opt/pgmodeler
PGMODELER_BINDIR=$PGMODELER_ROOT/bin
PGMODELER_PRIVATEBINDIR=$PGMODELER_ROOT/bin
PGMODELER_PRIVATELIBDIR=$PGMODELER_ROOT/lib
PGMODELER_SHARESDIR=$PGMODELER_ROOT/share
PGMODELER_DOCDIR=$PGMODELER_SHARESDIR/docs
PGMODELER_LANGDIR=$PGMODELER_SHARESDIR/lang
PGMODELER_CONFDIR=$PGMODELER_SHARESDIR/conf
PGMODELER_SAMPLESDIR=$PGMODELER_SHARESDIR/samples
PGMODELER_SCHEMASDIR=$PGMODELER_SHARESDIR/schemas
PGMODELER_PLUGINSDIR=$PGMODELER_PRIVATELIBDIR/plugins

build() {
    cd "$srcdir/$pkgname-${pkgver//_/-}"
    patch -p1 < ../patch_no_check_update.diff
     
    qmake-qt5 \
              PREFIX=$PGMODELER_ROOT BINDIR=$PGMODELER_BINDIR \
              PRIVATEBINDIR=$PGMODELER_PRIVATEBINDIR PRIVATELIBDIR=$PGMODELER_PRIVATELIBDIR \
              PLUGINSDIR=$PGMODELER_PLUGINSDIR DOCDIR=$PGMODELER_DOCDIR \
              CONFDIR=$PGMODELER_CONFDIR LANGDIR=$PGMODELER_LANGDIR \
              SAMPLESDIR=$PGMODELER_SAMPLESDIR SCHEMASDIR=$PGMODELER_SCHEMASDIR \
              -r pgmodeler.pro
    make || true

    # Temporary fix until Qt 5.12 is released, https://bugreports.qt.io/browse/QTBUG-65251
    cd libpgmodeler_ui/src
    for i in *.h
    do
        sed -i 's/setShortcut(QLatin1String/setShortcut(QKeySequence/' $i
    done
    cd -
    make
}

package() {
    cd "$srcdir/$pkgname-${pkgver//_/-}"
    make INSTALL_ROOT="${pkgdir}" install
    buildir="$srcdir/$pkgname-${pkgver//_/-}"

    # msg2 "Creating required dirs"
    mkdir -p "$pkgdir"/{etc/$pkgname,usr/{bin,share/{applications,icons/hicolor/64x64/{apps,mimetypes},licenses/$pkgname,$pkgname}}}
    mkdir -p "$pkgdir"$PGMODELER_DOCDIR "$pkgdir"$PGMODELER_LANGDIR "$pkgdir"$PGMODELER_SAMPLESDIR "$pkgdir"$PGMODELER_SCHEMASDIR "$pkgdir"$PGMODELER_PLUGINSDIR

    # msg2 "Moving stuff in place"
    # To be removed after 0.9-beta
    find $buildir -name connections.conf -exec sed -i 's/connect-timeout/connection-timeout/g' {} \;

    cp -R "$buildir"/conf/*.conf "$pkgdir/etc/$pkgname/"

    install -m755 "$srcdir/pgmodeler" "$pkgdir/usr/bin"
    install -m755 "$srcdir/pgmodeler-cli" "$pkgdir/usr/bin"
    install -m644 "$srcdir/pgmodeler_logo.png" "$pkgdir/usr/share/icons/hicolor/64x64/apps/pgmodeler.png"
    #install -m644 "$srcdir/pgmodeler_dbm.png" "$pkgdir/usr/share/icons/hicolor/64x64/mimetypes/TODO_REGISTER_MIME_ICON.png"
    install -m644 "$srcdir/pgmodeler.desktop" "$pkgdir/usr/share/applications"    
}

pkgname=jupyter-srv
pkgver=1.0.0
pkgrel=1
pkgdesc="JupyterLab service in /srv with dedicated system user and configurable env file"
arch=('any')
url="local"
license=('custom')
depends=('python' 'systemd')
makedepends=()
optdepends=('python-pip: needed at install/upgrade time to populate the venv')
install="${pkgname}.install"

source=(
  'jupyter-srv.service'
  'jupyter-srv.sysusers'
  'jupyter-srv.tmpfiles'
  'jupyter-srv.conf'
  'jupyter-srv-set-password'
)
sha256sums=(
  'SKIP'
  'SKIP'
  'SKIP'
  'SKIP'
  'SKIP'
)

package() {
  install -Dm644 jupyter-srv.service \
    "${pkgdir}/usr/lib/systemd/system/jupyter-srv.service"

  install -Dm644 jupyter-srv.sysusers \
    "${pkgdir}/usr/lib/sysusers.d/jupyter-srv.conf"

  install -Dm644 jupyter-srv.tmpfiles \
    "${pkgdir}/usr/lib/tmpfiles.d/jupyter-srv.conf"

  install -Dm644 jupyter-srv.conf \
    "${pkgdir}/usr/share/jupyter-srv/jupyter-srv.conf.example"

  install -Dm755 jupyter-srv-set-password \
    "${pkgdir}/usr/bin/jupyter-srv-set-password"
}

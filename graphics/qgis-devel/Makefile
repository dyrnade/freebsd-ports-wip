# New ports collection makefile for:	qgis
# Date created:		Apr 2006
# Whom:			Laurent Courty
#
# $FreeBSD: ports/graphics/qgis/Makefile,v 1.45 2011/05/09 16:42:23 bf Exp $
#

PORTNAME=	qgis
PORTVERSION=	1.7.0.r1
CATEGORIES=	graphics geography
MASTER_SITES=	https://download.github.com/
PKGNAMESUFFIX=	-devel
DISTNAME=	qgis-Quantum-GIS-694c356

MAINTAINER=	wen@FreeBSD.org
COMMENT=	Geographical Information System (GIS) data viewer

LIB_DEPENDS=	geos:${PORTSDIR}/graphics/geos \
		proj:${PORTSDIR}/graphics/proj \
		gsl.16:${PORTSDIR}/math/gsl \
		qwt.5:${PORTSDIR}/x11-toolkits/qwt5 \
		gdal:${PORTSDIR}/graphics/gdal

USE_PYTHON=	yes
USE_BISON=	build
USE_CMAKE=	yes
USE_QT_VER=	4
USE_SQLITE=	yes
CMAKE_USE_PTHREAD=	yes
CMAKE_ENV+=	QMAKESPEC=${QMAKESPEC}
QT_COMPONENTS=	gui svg xml network sql designer linguist_build \
		qmake_build moc_build rcc_build uic_build

GNU_CONFIGURE=	yes
CONFIGURE_ARGS=	--with-qtdir=${QTDIR}
USE_LDCONFIG=	yes

OPTIONS=	GRASS "Enable GRASS support" Off \
		POSTGIS "Enable PostGIS support" Off \
		MAPSERVER "Enable MapServer support" off \
		PYTHON "Enable Python support" On

.include <bsd.port.pre.mk>

.if !defined (QTDIR)
QTDIR=		${QT_PREFIX}
.endif

.if !defined (GRASS_INST_DIR)
GRASS_INST_DIR=	grass
.endif

.if defined(WITH_GRASS)
CONFIGURE_ARGS+=--with-grass=${LOCALBASE}/${GRASS_INST_DIR}
RUN_DEPENDS+=	${LOCALBASE}/lib/gdalplugins/gdal_GRASS.so:${PORTSDIR}/graphics/gdal-grass
.endif

.if defined(WITH_POSTGIS)
CONFIGURE_ARGS+=--with-postgresql
BUILD_DEPENDS+=	${LOCALBASE}/share/postgis:${PORTSDIR}/databases/postgis
PLIST_SUB+=	PGSQL=""
.else
CONFIGURE_ARGS+=--without-postgresql
PLIST_SUB+=	PGSQL="@comment "
.endif

.if defined(WITH_MAPSERVER)
PLIST_SUB+=	QGIS_FCGI=""
LIB_DEPENDS+=	fcgi.0:${PORTSDIR}/www/fcgi
.else
PLIST_SUB+=	QGIS_FCGI="@comment "
.endif

.if defined(WITHOUT_PYTHON)
PLIST_SUB+=	QGIS_PYTHON="@comment "
.else
PLIST_SUB+=	QGIS_PYTHON=""
BUILD_DEPENDS+=	${PYTHON_PKGNAMEPREFIX}sip>=4.8.2:${PORTSDIR}/devel/py-sip \
		${PYTHON_PKGNAMEPREFIX}qt4-gui>=4.5.4:${PORTSDIR}/x11-toolkits/py-qt4-gui \
		${PYTHON_PKGNAMEPREFIX}qt4-network>=4.5.4:${PORTSDIR}/net/py-qt4-network \
		${PYTHON_PKGNAMEPREFIX}qt4-svg>=4.5.4:${PORTSDIR}/graphics/py-qt4-svg \
		${PYTHON_PKGNAMEPREFIX}qt4-xml>=4.5.4:${PORTSDIR}/textproc/py-qt4-xml \
		${PYTHON_PKGNAMEPREFIX}qt4-core>=4.5.4:${PORTSDIR}/devel/py-qt4-core \
		${PYTHON_PKGNAMEPREFIX}sqlite3>=2.6.5:${PORTSDIR}/databases/py-sqlite3
.endif

MAN1=		qgis.1

post-patch:
		${REINPLACE_CMD} -e 's# dl##g' \
			${WRKSRC}/src/core/CMakeLists.txt

.if !defined(WITH_MAPSERVER)
		${REINPLACE_CMD} -e 's#WITH_MAPSERVER TRUE#WITH_MAPSERVER FALSE#g' \
			${WRKSRC}/CMakeLists.txt
.endif

.if defined(WITHOUT_PYTHON)
		${REINPLACE_CMD} -e 's#WITH_BINDINGS TRUE#WITH_BINDINGS FALSE#g' \
			${WRKSRC}/CMakeLists.txt
.endif

.include <bsd.port.post.mk>

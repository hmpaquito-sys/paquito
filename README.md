//# paquito

//#include "\prg\genlib\debug.ch"
#include "vf.ch"
#include "\prg\genlib\xtry.ch"

// Clase principal
CLASS ConsultaFactuSistemaFacturacion STATIC
   DATA oCabecera AS OBJECT
   DATA oFiltroConsulta AS OBJECT

   METHOD New() CONSTRUCTOR
   METHOD ToXml()
   METHOD lWriteXml()
   METHOD Validate()


   data holafecha  as date init nil
   data holastring as string init nil
   data holanumero as numeric init nil
   data holaobjeto as object init nil

ENDCLASS

METHOD New() CLASS ConsultaFactuSistemaFacturacion
//   ::oCabecera := Cabecera():New()


   ::oCabecera := Cabecera():New()
   ::oFiltroConsulta := FiltroConsulta():New()
RETURN Self

METHOD ToXml() CLASS ConsultaFactuSistemaFacturacion
   LOCAL cXml := ""
   cXml += '<?xml version="1.0" encoding="UTF-8"?>'+ CRLF
   cXml += '<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" '+ CRLF
   cXml += 'xmlns:sum="https://www2.agenciatributaria.gob.es/static_files/common/internet/dep/aplicaciones/es/aeat/tike/cont/ws/ConsultaLR.xsd" '+ CRLF
   cXml += 'xmlns:sum1="https://www2.agenciatributaria.gob.es/static_files/common/internet/dep/aplicaciones/es/aeat/tike/cont/ws/SuministroInformacion.xsd">'+ CRLF
   cXml += '<soapenv:Header/>'+ CRLF
   cXml += '<soapenv:Body>'+ CRLF
   cXml += '<sum:ConsultaFactuSistemaFacturacion>'+ CRLF
   cXml += ::oCabecera:ToXml()
   cXml += ::oFiltroConsulta:ToXml()
   cXml += '</sum:ConsultaFactuSistemaFacturacion>'+ CRLF
   cXml += '</soapenv:Body>'+ CRLF
   cXml += '</soapenv:Envelope>'+ CRLF

   cXML := vfFormatXml( cXml )

RETURN cXml

METHOD lWriteXml(cFile)

FLOGMSG_("LO ULTIMO", ::ToXml())
hb_MemoWrit(cFile, ::ToXml())
RETURN File(cFile)

METHOD Validate() CLASS ConsultaFactuSistemaFacturacion
   LOCAL aVal
   IF Empty(::oCabecera)
      RETURN { .F., { "ConsultaFactuSistemaFacturacion: oCabecera no puede estar vac+¡o", {{"::oCabecera", ::oCabecera}}, PROCNAME_ } }
   ENDIF
   aVal := ::oCabecera:Validate()
   IF !aVal[1]
      RETURN { .F., { "ConsultaFactuSistemaFacturacion: oCabecera no v+ílido" + ERROR_TRAZA } }
   ENDIF
   IF Empty(::oFiltroConsulta)
      RETURN { .F., { "ConsultaFactuSistemaFacturacion: oFiltroConsulta no puede estar vac+¡o", {{"::oFiltroConsulta", ::oFiltroConsulta}}, PROCNAME_ } }
   ENDIF
   aVal := ::oFiltroConsulta:Validate()
   IF !aVal[1]
      RETURN { .F., { "ConsultaFactuSistemaFacturacion: oFiltroConsulta no v+ílido" + ERROR_TRAZA } }
   ENDIF
RETURN { .T., NIL }

// Clase Cabecera
CLASS Cabecera STATIC
   DATA cIDVersion // AS CHARACTER
   DATA oObligadoEmision AS OBJECT

   METHOD New() CONSTRUCTOR
   METHOD ToXml()
   METHOD Validate()
ENDCLASS

METHOD New() CLASS Cabecera
   ::cIDVersion := "1.0"
   ::oObligadoEmision := ObligadoEmision():New()
RETURN Self

METHOD ToXml() CLASS Cabecera
   LOCAL cXml := ""
   cXml += '<sum:Cabecera>'+ CRLF
   cXml += '<sum1:IDVersion>' + ::cIDVersion + '</sum1:IDVersion>'+ CRLF
   cXml += ::oObligadoEmision:ToXml()
   cXml += '</sum:Cabecera>'+ CRLF

   // AMM Al 01/08/25
   cXML:= hb_StrToUTF8(cXML, "ESWIN")       // Asumo que los datos en las .dbfs estan en ANSI

RETURN cXml

METHOD Validate() CLASS Cabecera
   LOCAL aVal
   IF Empty(::cIDVersion)
      RETURN { .F., { "Cabecera: cIDVersion no puede estar vac+¡o", {{"::cIDVersion", ::cIDVersion}}, PROCNAME_ } }
   ENDIF
   IF Len(::cIDVersion) > 10
      RETURN { .F., { "Cabecera: cIDVersion excede los 10 caracteres", {{"::cIDVersion", ::cIDVersion}}, PROCNAME_ } }
   ENDIF
   IF Empty(::oObligadoEmision)
      RETURN { .F., { "Cabecera: oObligadoEmision no puede estar vac+¡o", {{"::oObligadoEmision", ::oObligadoEmision}}, PROCNAME_ } }
   ENDIF
   aVal := ::oObligadoEmision:Validate()
   IF !aVal[1]
      RETURN { .F., { "Cabecera: oObligadoEmision no v+ílido" + ERROR_TRAZA } }
   ENDIF
RETURN { .T., NIL }

// Clase ObligadoEmision
CLASS ObligadoEmision STATIC
   DATA cNombreRazon // AS CHARACTER
   DATA cNIF // AS CHARACTER

   METHOD New() CONSTRUCTOR
   METHOD ToXml()
   METHOD Validate()
ENDCLASS

METHOD New() CLASS ObligadoEmision
   ::cNombreRazon := ""
   ::cNIF := ""
RETURN Self

METHOD ToXml() CLASS ObligadoEmision
   LOCAL cXml := ""
   cXml += '<sum1:ObligadoEmision>'+ CRLF
   cXml += '<sum1:NombreRazon>' + ::cNombreRazon + '</sum1:NombreRazon>'+ CRLF
   cXml += '<sum1:NIF>' + ::cNIF + '</sum1:NIF>'+ CRLF
   cXml += '</sum1:ObligadoEmision>'+ CRLF
RETURN cXml

METHOD Validate() CLASS ObligadoEmision
   LOCAL aVal
   IF Empty(::cNombreRazon)
      RETURN { .F., { "ObligadoEmision: cNombreRazon no puede estar vac+¡o", {{"::cNombreRazon", ::cNombreRazon}}, PROCNAME_ } }
   ENDIF
   IF Len(::cNombreRazon) > 120
      RETURN { .F., { "ObligadoEmision: cNombreRazon excede los 120 caracteres", {{"::cNombreRazon", ::cNombreRazon}}, PROCNAME_ } }
   ENDIF
   IF Empty(::cNIF)
      RETURN { .F., { "ObligadoEmision: cNIF no puede estar vac+¡o", {{"::cNIF", ::cNIF}}, PROCNAME_ } }
   ENDIF
   IF Len(::cNIF) <> 9
      RETURN { .F., { "ObligadoEmision: cNIF debe tener 9 caracteres", {{"::cNIF", ::cNIF}}, PROCNAME_ } }
   ENDIF
   IF !HB_RegExMatch( "[A-Za-z0-9]{9}", ::cNIF )
      RETURN { .F., { "ObligadoEmision: cNIF debe ser alfanum+®rico", {{"::cNIF", ::cNIF}}, PROCNAME_ } }
   ENDIF
   aVal := vfValidaNIF( ::cNIF )
   IF !aVal[1]
      RETURN { .F., { "ObligadoEmision: cNIF no v+ílido" + ERROR_TRAZA } }
   ENDIF
RETURN { .T., NIL }

// Clase FiltroConsulta
CLASS FiltroConsulta STATIC
   DATA oPeriodoImputacion AS OBJECT
   DATA oFechaExpedicionFactura AS OBJECT
   DATA oSistemaInformatico AS OBJECT
   DATA oClavePaginacion AS OBJECT
   DATA oContraparte AS OBJECT

   METHOD New() CONSTRUCTOR
   METHOD ToXml()
   METHOD Validate()
ENDCLASS

METHOD New() CLASS FiltroConsulta
   ::oPeriodoImputacion := NIL
   ::oFechaExpedicionFactura := NIL
   ::oSistemaInformatico := NIL
   ::oClavePaginacion := NIL
   ::oContraparte := NIL
RETURN Self

METHOD ToXml() CLASS FiltroConsulta
   LOCAL cXml := ""
   cXml += '<sum:FiltroConsulta>'+ CRLF
   IF !ISNIL(::oPeriodoImputacion)
      cXml += ::oPeriodoImputacion:ToXml()
   ENDIF
   IF !ISNIL(::oFechaExpedicionFactura)
      cXml += ::oFechaExpedicionFactura:ToXml()
   ENDIF
   IF !ISNIL(::oSistemaInformatico)
      cXml += ::oSistemaInformatico:ToXml()
   ENDIF
   IF !ISNIL(::oClavePaginacion)
      cXml += ::oClavePaginacion:ToXml()
   ENDIF
   IF !ISNIL(::oContraparte)
      cXml += ::oContraparte:ToXml()
   ENDIF
   cXml += '</sum:FiltroConsulta>'+ CRLF
RETURN cXml

METHOD Validate() CLASS FiltroConsulta
   LOCAL aVal
   IF ISNIL(::oPeriodoImputacion) .AND. ISNIL(::oFechaExpedicionFactura) .AND. ISNIL(::oContraparte)
      RETURN { .F., { "FiltroConsulta: Debe haber al menos un filtro (PeriodoImputacion, FechaExpedicionFactura o Contraparte)", NIL, PROCNAME_ } }
   ENDIF
   IF !ISNIL(::oPeriodoImputacion)
      aVal := ::oPeriodoImputacion:Validate()
      IF !aVal[1]
         RETURN { .F., { "FiltroConsulta: oPeriodoImputacion no v+ílido" + ERROR_TRAZA } }
      ENDIF
   ENDIF
   IF !ISNIL(::oFechaExpedicionFactura)
      aVal := ::oFechaExpedicionFactura:Validate()
      IF !aVal[1]
         RETURN { .F., { "FiltroConsulta: oFechaExpedicionFactura no v+ílido" + ERROR_TRAZA } }
      ENDIF
   ENDIF
   IF !ISNIL(::oSistemaInformatico)
      aVal := ::oSistemaInformatico:Validate()
      IF !aVal[1]
         RETURN { .F., { "FiltroConsulta: oSistemaInformatico no v+ílido" + ERROR_TRAZA } }
      ENDIF
   ENDIF
   IF !ISNIL(::oClavePaginacion)
      aVal := ::oClavePaginacion:Validate()
      IF !aVal[1]
         RETURN { .F., { "FiltroConsulta: oClavePaginacion no v+ílido" + ERROR_TRAZA } }
      ENDIF
   ENDIF
   IF !ISNIL(::oContraparte)
      aVal := ::oContraparte:Validate()
      IF !aVal[1]
         RETURN { .F., { "FiltroConsulta: oContraparte no v+ílido" + ERROR_TRAZA } }
      ENDIF
   ENDIF
RETURN { .T., NIL }

// Clase PeriodoImputacion
CLASS PeriodoImputacion STATIC
   DATA cEjercicio // AS CHARACTER
   DATA cPeriodo // AS CHARACTER

   METHOD New() CONSTRUCTOR
   METHOD ToXml()
   METHOD Validate()
ENDCLASS

METHOD New() CLASS PeriodoImputacion
   ::cEjercicio := ""
   ::cPeriodo := ""
RETURN Self

METHOD ToXml() CLASS PeriodoImputacion
   LOCAL cXml := ""
   cXml += '<sum:PeriodoImputacion>'+ CRLF
   cXml += '<sum1:Ejercicio>' + ::cEjercicio + '</sum1:Ejercicio>'+ CRLF
   cXml += '<sum1:Periodo>' + ::cPeriodo + '</sum1:Periodo>'+ CRLF
   cXml += '</sum:PeriodoImputacion>'+ CRLF
RETURN cXml

METHOD Validate() CLASS PeriodoImputacion
   LOCAL aVal
   IF Empty(::cEjercicio)
      RETURN { .F., { "PeriodoImputacion: cEjercicio no puede estar vac+¡o", {{"::cEjercicio", ::cEjercicio}}, PROCNAME_ } }
   ENDIF
   IF Len(::cEjercicio) <> 4
      RETURN { .F., { "PeriodoImputacion: cEjercicio debe tener 4 caracteres", {{"::cEjercicio", ::cEjercicio}}, PROCNAME_ } }
   ENDIF
   IF !HB_RegExMatch( "[0-9]{4}", ::cEjercicio )
      RETURN { .F., { "PeriodoImputacion: cEjercicio debe ser num+®rico", {{"::cEjercicio", ::cEjercicio}}, PROCNAME_ } }
   ENDIF
   IF Empty(::cPeriodo)
      RETURN { .F., { "PeriodoImputacion: cPeriodo no puede estar vac+¡o", {{"::cPeriodo", ::cPeriodo}}, PROCNAME_ } }
   ENDIF
   IF Len(::cPeriodo) <> 2
      RETURN { .F., { "PeriodoImputacion: cPeriodo debe tener 2 caracteres", {{"::cPeriodo", ::cPeriodo}}, PROCNAME_ } }
   ENDIF
   IF !HB_RegExMatch( "[0-1][0-9]", ::cPeriodo ) .OR. Val(::cPeriodo) < 1 .OR. Val(::cPeriodo) > 12
      RETURN { .F., { "PeriodoImputacion: cPeriodo debe ser un mes v+ílido (01-12)", {{"::cPeriodo", ::cPeriodo}}, PROCNAME_ } }
   ENDIF
RETURN { .T., NIL }

// Clase FechaExpedicionFactura
CLASS FechaExpedicionFactura STATIC
   DATA dFecha //AS DATE INIT NIL
   DATA dDesde //AS DATE INIT NIL
   DATA dHasta //AS DATE INIT NIL

   METHOD New() CONSTRUCTOR
   METHOD ToXml()
   METHOD Validate()
ENDCLASS

METHOD New() CLASS FechaExpedicionFactura

FLOGMSG_("ANTES DE", ::dFecha, ::dDesde, ::dHasta)
   ::dFecha := NIL
   ::dDesde := NIL
   ::dHasta := NIL
RETURN Self

METHOD ToXml() CLASS FechaExpedicionFactura
   LOCAL cXml := ""

   FLOGMSG_("REVISANDO FECHAS", ::dFecha, ::dDesde, ::dHasta)

   cXml += '<sum:FechaExpedicionFactura>'+ CRLF
   IF !ISNIL(::dFecha)
      cXml += '<sum1:FechaExpedicionFactura>' + hb_DToC(::dFecha, "dd-mm-yyyy") + '</sum1:FechaExpedicionFactura>'+ CRLF
   ELSEIF !ISNIL(::dDesde) .AND. !ISNIL(::dHasta)
      cXml += '<sum1:RangoFechaExpedicion>'+ CRLF
      cXml += '<sum1:Desde>' + hb_DToC(::dDesde, "dd-mm-yyyy") + '</sum1:Desde>'+ CRLF
      cXml += '<sum1:Hasta>' + hb_DToC(::dHasta, "dd-mm-yyyy") + '</sum1:Hasta>'+ CRLF
      cXml += '</sum1:RangoFechaExpedicion>'+ CRLF
   ENDIF
   cXml += '</sum:FechaExpedicionFactura>'+ CRLF
RETURN cXml

METHOD Validate() CLASS FechaExpedicionFactura
   LOCAL aVal
   // Caso 1: Todo NIL, no se especific+¦ nada
   IF ISNIL(::dFecha) .AND. ISNIL(::dDesde) .AND. ISNIL(::dHasta)
      RETURN { .F., { "FechaExpedicionFactura: Todas las fechas (dFecha, dDesde, dHasta) son NIL, debe especificarse una fecha o un rango v+ílido", NIL, PROCNAME_ } }
   ENDIF
   // Caso 2: Todo vac+¡o, no se especific+¦ nada v+ílido
   IF Empty(::dFecha) .AND. Empty(::dDesde) .AND. Empty(::dHasta)
      RETURN { .F., { "FechaExpedicionFactura: Todas las fechas (dFecha, dDesde, dHasta) est+ín vac+¡as, debe especificarse una fecha o un rango v+ílido", NIL, PROCNAME_ } }
   ENDIF
   // Caso 3: Fecha +¦nica presente
   IF !Empty(::dFecha)
      IF !Empty(::dDesde) .OR. !Empty(::dHasta)
         RETURN { .F., { "FechaExpedicionFactura: No se permite combinar dFecha con dDesde o dHasta", {{"::dFecha", ::dFecha}, {"::dDesde", ::dDesde}, {"::dHasta", ::dHasta}}, PROCNAME_ } }
      ENDIF
      IF ::dFecha > DATE()
         RETURN { .F., { "FechaExpedicionFactura: dFecha no puede ser futura", {{"::dFecha", ::dFecha}}, PROCNAME_ } }
      ENDIF
   ELSE
      // Caso 4: Rango vac+¡o (ambos vac+¡os)
      IF Empty(::dDesde) .AND. Empty(::dHasta)
         RETURN { .F., { "FechaExpedicionFactura: dDesde y dHasta est+ín vac+¡os, el rango debe ser completo y v+ílido", {{"::dDesde", ::dDesde}, {"::dHasta", ::dHasta}}, PROCNAME_ } }
      ENDIF
      // Caso 5: Rango (ambos deben estar presentes o ninguno, pero ya cubierto arriba como "todo vac+¡o")
      IF Empty(::dDesde) .AND. !Empty(::dHasta)
         RETURN { .F., { "FechaExpedicionFactura: dDesde est+í vac+¡o pero dHasta no, el rango debe ser completo", {{"::dDesde", ::dDesde}, {"::dHasta", ::dHasta}}, PROCNAME_ } }
      ENDIF
      IF !Empty(::dDesde) .AND. Empty(::dHasta)
         RETURN { .F., { "FechaExpedicionFactura: dHasta est+í vac+¡o pero dDesde no, el rango debe ser completo", {{"::dDesde", ::dDesde}, {"::dHasta", ::dHasta}}, PROCNAME_ } }
      ENDIF
      IF !Empty(::dDesde) .AND. !Empty(::dHasta)
         IF ::dDesde > ::dHasta
            RETURN { .F., { "FechaExpedicionFactura: dDesde no puede ser mayor que dHasta", {{"::dDesde", ::dDesde}, {"::dHasta", ::dHasta}}, PROCNAME_ } }
         ENDIF
         IF ::dHasta > DATE()
            RETURN { .F., { "FechaExpedicionFactura: dHasta no puede ser futura", {{"::dHasta", ::dHasta}}, PROCNAME_ } }
         ENDIF
      ELSE
         RETURN { .F., { "FechaExpedicionFactura: Error inesperado", {{"::dDesde", ::dDesde}, {"::dHasta", ::dHasta}}, PROCNAME_ } }
      ENDIF
   ENDIF
RETURN { .T., NIL }


// Clase SistemaInformatico
CLASS SistemaInformatico STATIC
   DATA cNombreRazon // AS CHARACTER
   DATA cNIF // AS CHARACTER
   DATA cNombreSistemaInformatico // AS CHARACTER
   DATA cIdSistemaInformatico // AS CHARACTER
   DATA cVersion // AS CHARACTER
   DATA cNumeroInstalacion // AS CHARACTER
   DATA cTipoUsoPosibleSoloVerifactu // AS CHARACTER
   DATA cTipoUsoPosibleMultiOT // AS CHARACTER
   DATA cIndicadorMultiplesOT // AS CHARACTER

   METHOD New() CONSTRUCTOR
   METHOD ToXml()
   METHOD Validate()
ENDCLASS

METHOD New() CLASS SistemaInformatico
   ::cNombreRazon := ""
   ::cNIF := ""
   ::cNombreSistemaInformatico := ""
   ::cIdSistemaInformatico := ""
   ::cVersion := ""
   ::cNumeroInstalacion := ""
   ::cTipoUsoPosibleSoloVerifactu := "S"
   ::cTipoUsoPosibleMultiOT := "N"
   ::cIndicadorMultiplesOT := "N"
RETURN Self

METHOD ToXml() CLASS SistemaInformatico
   LOCAL cXml := ""
   cXml += '<sum:SistemaInformatico>'+ CRLF
   cXml += '<sum1:NombreRazon>' + ::cNombreRazon + '</sum1:NombreRazon>'+ CRLF
   cXml += '<sum1:NIF>' + ::cNIF + '</sum1:NIF>'+ CRLF
   cXml += '<sum1:NombreSistemaInformatico>' + ::cNombreSistemaInformatico + '</sum1:NombreSistemaInformatico>'+ CRLF
   cXml += '<sum1:IdSistemaInformatico>' + ::cIdSistemaInformatico + '</sum1:IdSistemaInformatico>'+ CRLF
   cXml += '<sum1:Version>' + ::cVersion + '</sum1:Version>'+ CRLF
   cXml += '<sum1:NumeroInstalacion>' + ::cNumeroInstalacion + '</sum1:NumeroInstalacion>'+ CRLF
   cXml += '<sum1:TipoUsoPosibleSoloVerifactu>' + ::cTipoUsoPosibleSoloVerifactu + '</sum1:TipoUsoPosibleSoloVerifactu>'+ CRLF
   cXml += '<sum1:TipoUsoPosibleMultiOT>' + ::cTipoUsoPosibleMultiOT + '</sum1:TipoUsoPosibleMultiOT>'+ CRLF
   cXml += '<sum1:IndicadorMultiplesOT>' + ::cIndicadorMultiplesOT + '</sum1:IndicadorMultiplesOT>'+ CRLF
   cXml += '</sum:SistemaInformatico>'+ CRLF
RETURN cXml

METHOD Validate() CLASS SistemaInformatico
   LOCAL aVal
   IF Empty(::cNombreRazon)
      RETURN { .F., { "SistemaInformatico: cNombreRazon no puede estar vac+¡o", {{"::cNombreRazon", ::cNombreRazon}}, PROCNAME_ } }
   ENDIF
   IF Len(::cNombreRazon) > 120
      RETURN { .F., { "SistemaInformatico: cNombreRazon excede los 120 caracteres", {{"::cNombreRazon", ::cNombreRazon}}, PROCNAME_ } }
   ENDIF
   IF Empty(::cNIF)
      RETURN { .F., { "SistemaInformatico: cNIF no puede estar vac+¡o", {{"::cNIF", ::cNIF}}, PROCNAME_ } }
   ENDIF
   IF Len(::cNIF) <> 9
      RETURN { .F., { "SistemaInformatico: cNIF debe tener 9 caracteres", {{"::cNIF", ::cNIF}}, PROCNAME_ } }
   ENDIF
   IF !HB_RegExMatch( "[A-Za-z0-9]{9}", ::cNIF )
      RETURN { .F., { "SistemaInformatico: cNIF debe ser alfanum+®rico", {{"::cNIF", ::cNIF}}, PROCNAME_ } }
   ENDIF
   aVal := vfValidaNIF( ::cNIF )
   IF !aVal[1]
      RETURN { .F., { "SistemaInformatico: cNIF no v+ílido" + ERROR_TRAZA } }
   ENDIF
   IF Empty(::cNombreSistemaInformatico)
      RETURN { .F., { "SistemaInformatico: cNombreSistemaInformatico no puede estar vac+¡o", {{"::cNombreSistemaInformatico", ::cNombreSistemaInformatico}}, PROCNAME_ } }
   ENDIF
   IF Len(::cNombreSistemaInformatico) > 120
      RETURN { .F., { "SistemaInformatico: cNombreSistemaInformatico excede los 120 caracteres", {{"::cNombreSistemaInformatico", ::cNombreSistemaInformatico}}, PROCNAME_ } }
   ENDIF
   IF Empty(::cIdSistemaInformatico)
      RETURN { .F., { "SistemaInformatico: cIdSistemaInformatico no puede estar vac+¡o", {{"::cIdSistemaInformatico", ::cIdSistemaInformatico}}, PROCNAME_ } }
   ENDIF
   IF Len(::cIdSistemaInformatico) > 20
      RETURN { .F., { "SistemaInformatico: cIdSistemaInformatico excede los 20 caracteres", {{"::cIdSistemaInformatico", ::cIdSistemaInformatico}}, PROCNAME_ } }
   ENDIF
   IF Empty(::cVersion)
      RETURN { .F., { "SistemaInformatico: cVersion no puede estar vac+¡o", {{"::cVersion", ::cVersion}}, PROCNAME_ } }
   ENDIF
   IF Len(::cVersion) > 20
      RETURN { .F., { "SistemaInformatico: cVersion excede los 20 caracteres", {{"::cVersion", ::cVersion}}, PROCNAME_ } }
   ENDIF
   IF Empty(::cNumeroInstalacion)
      RETURN { .F., { "SistemaInformatico: cNumeroInstalacion no puede estar vac+¡o", {{"::cNumeroInstalacion", ::cNumeroInstalacion}}, PROCNAME_ } }
   ENDIF
   IF Len(::cNumeroInstalacion) > 40
      RETURN { .F., { "SistemaInformatico: cNumeroInstalacion excede los 40 caracteres", {{"::cNumeroInstalacion", ::cNumeroInstalacion}}, PROCNAME_ } }
   ENDIF
   IF !INLIST(::cTipoUsoPosibleSoloVerifactu, "S", "N")
      RETURN { .F., { "SistemaInformatico: cTipoUsoPosibleSoloVerifactu debe ser S o N", {{"::cTipoUsoPosibleSoloVerifactu", ::cTipoUsoPosibleSoloVerifactu}}, PROCNAME_ } }
   ENDIF
   IF !INLIST(::cTipoUsoPosibleMultiOT, "S", "N")
      RETURN { .F., { "SistemaInformatico: cTipoUsoPosibleMultiOT debe ser S o N", {{"::cTipoUsoPosibleMultiOT", ::cTipoUsoPosibleMultiOT}}, PROCNAME_ } }
   ENDIF
   IF !INLIST(::cIndicadorMultiplesOT, "S", "N")
      RETURN { .F., { "SistemaInformatico: cIndicadorMultiplesOT debe ser S o N", {{"::cIndicadorMultiplesOT", ::cIndicadorMultiplesOT}}, PROCNAME_ } }
   ENDIF
RETURN { .T., NIL }

// Clase ClavePaginacion
CLASS ClavePaginacion STATIC
   DATA cIDEmisorFactura //// AS CHARACTER
   DATA cNumSerieFactura //// AS CHARACTER
   DATA dFechaExpedicionFactura //AS DATE INIT NIL

   METHOD New() CONSTRUCTOR
   METHOD ToXml()
   METHOD Validate()
ENDCLASS

METHOD New() CLASS ClavePaginacion
   ::cIDEmisorFactura := ""
   ::cNumSerieFactura := ""
//   ::dFechaExpedicionFactura := NIL
RETURN Self

METHOD ToXml() CLASS ClavePaginacion
   LOCAL cXml := ""
   cXml += '<sum:ClavePaginacion>'+ CRLF
   cXml += '<sum1:IDEmisorFactura>' + ::cIDEmisorFactura + '</sum1:IDEmisorFactura>'+ CRLF
   cXml += '<sum1:NumSerieFactura>' + ::cNumSerieFactura + '</sum1:NumSerieFactura>'+ CRLF
   cXml += '<sum1:FechaExpedicionFactura>' + hb_DToC(::dFechaExpedicionFactura, "dd-mm-yyyy") + '</sum1:FechaExpedicionFactura>'+ CRLF
   cXml += '</sum:ClavePaginacion>'+ CRLF
RETURN cXml

METHOD Validate() CLASS ClavePaginacion
   LOCAL aVal
   IF Empty(::cIDEmisorFactura)
      RETURN { .F., { "ClavePaginacion: cIDEmisorFactura no puede estar vac+¡o", {{"::cIDEmisorFactura", ::cIDEmisorFactura}}, PROCNAME_ } }
   ENDIF
   IF Len(::cIDEmisorFactura) <> 9
      RETURN { .F., { "ClavePaginacion: cIDEmisorFactura debe tener 9 caracteres", {{"::cIDEmisorFactura", ::cIDEmisorFactura}}, PROCNAME_ } }
   ENDIF
   IF !HB_RegExMatch( "[A-Za-z0-9]{9}", ::cIDEmisorFactura )
      RETURN { .F., { "ClavePaginacion: cIDEmisorFactura debe ser alfanum+®rico", {{"::cIDEmisorFactura", ::cIDEmisorFactura}}, PROCNAME_ } }
   ENDIF
   aVal := vfValidaNIF( ::cIDEmisorFactura )
   IF !aVal[1]
      RETURN { .F., { "ClavePaginacion: cIDEmisorFactura no v+ílido" + ERROR_TRAZA } }
   ENDIF
   IF Empty(::cNumSerieFactura)
      RETURN { .F., { "ClavePaginacion: cNumSerieFactura no puede estar vac+¡o", {{"::cNumSerieFactura", ::cNumSerieFactura}}, PROCNAME_ } }
   ENDIF
   IF Len(::cNumSerieFactura) > 60
      RETURN { .F., { "ClavePaginacion: cNumSerieFactura excede los 60 caracteres", {{"::cNumSerieFactura", ::cNumSerieFactura}}, PROCNAME_ } }
   ENDIF
   IF ISNIL(::dFechaExpedicionFactura)
      RETURN { .F., { "ClavePaginacion: dFechaExpedicionFactura no est+í inicializada (NIL)", {{"::dFechaExpedicionFactura", ::dFechaExpedicionFactura}}, PROCNAME_ } }
   ENDIF
   IF Empty(::dFechaExpedicionFactura)
      RETURN { .F., { "ClavePaginacion: dFechaExpedicionFactura est+í vac+¡a", {{"::dFechaExpedicionFactura", ::dFechaExpedicionFactura}}, PROCNAME_ } }
   ENDIF
   IF ::dFechaExpedicionFactura > DATE()
      RETURN { .F., { "ClavePaginacion: dFechaExpedicionFactura no puede ser futura", {{"::dFechaExpedicionFactura", ::dFechaExpedicionFactura}}, PROCNAME_ } }
   ENDIF
RETURN { .T., NIL }



// Clase Contraparte
CLASS Contraparte STATIC
   DATA cNombreRazon // AS CHARACTER
   DATA cNIF // AS CHARACTER

   METHOD New() CONSTRUCTOR
   METHOD ToXml()
   METHOD Validate()
ENDCLASS

METHOD New() CLASS Contraparte
   ::cNombreRazon := ""
   ::cNIF := ""
RETURN Self

METHOD ToXml() CLASS Contraparte
   LOCAL cXml := ""
   cXml += '<sum:Contraparte>'+ CRLF
   cXml += '<sum1:NombreRazon>' + ::cNombreRazon + '</sum1:NombreRazon>'+ CRLF
   cXml += '<sum1:NIF>' + ::cNIF + '</sum1:NIF>'+ CRLF
   cXml += '</sum:Contraparte>'+ CRLF
RETURN cXml

METHOD Validate() CLASS Contraparte
   LOCAL aVal
   IF Empty(::cNombreRazon)
      RETURN { .F., { "Contraparte: cNombreRazon no puede estar vac+¡o", {{"::cNombreRazon", ::cNombreRazon}}, PROCNAME_ } }
   ENDIF
   IF Len(::cNombreRazon) > 120
      RETURN { .F., { "Contraparte: cNombreRazon excede los 120 caracteres", {{"::cNombreRazon", ::cNombreRazon}}, PROCNAME_ } }
   ENDIF
   IF Empty(::cNIF)
      RETURN { .F., { "Contraparte: cNIF no puede estar vac+¡o", {{"::cNIF", ::cNIF}}, PROCNAME_ } }
   ENDIF
   IF Len(::cNIF) <> 9
      RETURN { .F., { "Contraparte: cNIF debe tener 9 caracteres", {{"::cNIF", ::cNIF}}, PROCNAME_ } }
   ENDIF
   IF !HB_RegExMatch( "[A-Za-z0-9]{9}", ::cNIF )
      RETURN { .F., { "Contraparte: cNIF debe ser alfanum+®rico", {{"::cNIF", ::cNIF}}, PROCNAME_ } }
   ENDIF
   aVal := vfValidaNIF( ::cNIF )
   IF !aVal[1]
      RETURN { .F., { "Contraparte: cNIF no v+ílido" + ERROR_TRAZA } }
   ENDIF
RETURN { .T., NIL }


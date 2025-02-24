# Caso-de-Estudio-22
Sistema de Gestión de Alquiler de Equipos Deportivos para una Tienda Especializada "Deportes Extremos"

Tablas Principales

**Equipos**

Almacena la información de los equipos deportivos disponibles para alquiler 

CREATE TABLE Equipos (
id SERIAL PRIMARY KEY,
codigo_inventario VARCHAR(50) UNIQUE NOT NULL,
tipo_equipo VARCHAR(50) NOT NULL,
marca VARCHAR(50),
modelo VARCHAR(50),
talla VARCHAR(20),
estado VARCHAR(20) CHECK (estado IN ('disponible', 'alquilado', 'en_mantenimiento')),
fecha_ultima_revision DATE,
observaciones TEXT
);

**Clientes**

** Almacena la información de los clientes que alquilan equipos **

CREATE TABLE Clientes (
id SERIAL PRIMARY KEY,
nombre VARCHAR(100) NOT NULL,
apellidos VARCHAR(100) NOT NULL,
direccion TEXT,
telefono VARCHAR(20),
email VARCHAR(100) UNIQUE NOT NULL,
dni_nie VARCHAR(20) UNIQUE NOT NULL,
nivel_experiencia VARCHAR(20) CHECK (nivel_experiencia IN ('principiante', 'intermedio', 'avanzado'))
);

**Alquileres**

** Registra los alquileres realizados por los clientes **

CREATE TABLE Alquileres (
id SERIAL PRIMARY KEY,
cliente_id INT REFERENCES Clientes(id) ON DELETE CASCADE,
fecha_recogida TIMESTAMP NOT NULL,
fecha_devolucion TIMESTAMP NOT NULL,
precio_total NUMERIC(10, 2),
estado VARCHAR(20) CHECK (estado IN ('pendiente', 'en_curso', 'finalizado', 'devuelto_con_danos'))
);

**Alquiler_Equipos**

** Relación muchos a muchos entre Alquileres y Equipos **

CREATE TABLE Alquiler_Equipos (
alquiler_id INT REFERENCES Alquileres(id) ON DELETE CASCADE,
equipo_id INT REFERENCES Equipos(id) ON DELETE CASCADE,
PRIMARY KEY (alquiler_id, equipo_id)
);

**Contratos**

** Almacena los contratos firmados por los clientes **

CREATE TABLE Contratos (
id SERIAL PRIMARY KEY,
alquiler_id INT REFERENCES Alquileres(id) ON DELETE CASCADE,
fecha_firma DATE NOT NULL,
condiciones TEXT,
clausulas_seguridad TEXT,
contacto_emergencia VARCHAR(100)
);

**Pagos**

** Registra los pagos realizados por los clientes **

CREATE TABLE Pagos (
id SERIAL PRIMARY KEY,
alquiler_id INT REFERENCES Alquileres(id) ON DELETE CASCADE,
monto NUMERIC(10, 2) NOT NULL,
metodo_pago VARCHAR(50),
fecha_pago TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

**Mantenimiento**

** Registra el mantenimiento y reparaciones de los equipos **

CREATE TABLE Mantenimiento (
id SERIAL PRIMARY KEY,
equipo_id INT REFERENCES Equipos(id) ON DELETE CASCADE,
tipo_mantenimiento VARCHAR(50) CHECK (tipo_mantenimiento IN ('preventivo', 'correctivo')),
fecha_inicio DATE NOT NULL,
fecha_fin DATE,
taller_tecnico VARCHAR(100),
costo NUMERIC(10, 2),
descripcion TEXT
);

=====================================================================================================================================

**Consultas SQL**

** Obtener todos los equipos disponibles para alquiler **

SELECT * FROM Equipos WHERE estado = 'disponible';

** Obtener el historial de alquileres de un cliente **

SELECT a.id, a.fecha_recogida, a.fecha_devolucion, a.precio_total
FROM Alquileres a
JOIN Clientes c ON a.cliente_id = c.id
WHERE c.email = 'cliente@example.com';

** Calcular el total de ingresos por alquileres en un mes **

SELECT SUM(p.monto) AS total_ingresos
FROM Pagos p
JOIN Alquileres a ON p.alquiler_id = a.id
WHERE EXTRACT(MONTH FROM a.fecha_recogida) = 10 AND EXTRACT(YEAR FROM a.fecha_recogida) = 2023;

** Obtener el historial de mantenimiento de un equipo **

SELECT m.tipo_mantenimiento, m.fecha_inicio, m.fecha_fin, m.descripcion
FROM Mantenimiento m
JOIN Equipos e ON m.equipo_id = e.id
WHERE e.codigo_inventario = 'EQ12345';

** Generar un informe de equipos alquilados en una fecha específica **

SELECT e.codigo_inventario, e.tipo_equipo, e.marca, e.modelo
FROM Equipos e
JOIN Alquiler_Equipos ae ON e.id = ae.equipo_id
JOIN Alquileres a ON ae.alquiler_id = a.id
WHERE a.fecha_recogida = '2023-10-15';

``

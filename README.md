# Taller 3 DB Garden

### Creación de la DB y sus Tablas

```mysql
-- MySQL Workbench Forward Engineering

SET @OLD_UNIQUE_CHECKS=@@UNIQUE_CHECKS, UNIQUE_CHECKS=0;
SET @OLD_FOREIGN_KEY_CHECKS=@@FOREIGN_KEY_CHECKS, FOREIGN_KEY_CHECKS=0;
SET @OLD_SQL_MODE=@@SQL_MODE, SQL_MODE='ONLY_FULL_GROUP_BY,STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_ENGINE_SUBSTITUTION';

-- -----------------------------------------------------
-- Schema GARDEN_DB
-- -----------------------------------------------------

-- -----------------------------------------------------
-- Schema GARDEN_DB
-- -----------------------------------------------------
CREATE SCHEMA IF NOT EXISTS `GARDEN_DB` DEFAULT CHARACTER SET utf8 ;
USE `GARDEN_DB` ;

-- -----------------------------------------------------
-- Table `GARDEN_DB`.`gama_producto`
-- -----------------------------------------------------
CREATE TABLE IF NOT EXISTS `GARDEN_DB`.`gama_producto` (
  `codigo_gama` INT(11) NOT NULL,
  `gama` VARCHAR(50) NOT NULL,
  `descripcion_texto` TEXT(50) NULL,
  `descripcion_html` TEXT(50) NULL,
  `imagen` VARCHAR(256) NULL,
  PRIMARY KEY (`codigo_gama`))
ENGINE = InnoDB;


-- -----------------------------------------------------
-- Table `GARDEN_DB`.`dimensiones`
-- -----------------------------------------------------
CREATE TABLE IF NOT EXISTS `GARDEN_DB`.`dimensiones` (
  `codigo_dimensiones` VARCHAR(15) NOT NULL,
  `largo` VARCHAR(7) NULL,
  `alto` VARCHAR(7) NULL,
  `ancho` VARCHAR(7) NULL,
  `peso` VARCHAR(7) NULL,
  PRIMARY KEY (`codigo_dimensiones`))
ENGINE = InnoDB;


-- -----------------------------------------------------
-- Table `GARDEN_DB`.`producto`
-- -----------------------------------------------------
CREATE TABLE IF NOT EXISTS `GARDEN_DB`.`producto` (
  `codigo_producto` VARCHAR(15) NOT NULL,
  `nombre` VARCHAR(70) NOT NULL,
  `codigo_gama` INT(11) NOT NULL,
  `cantidad_stock` SMALLINT(6) NOT NULL,
  `precio_venta` DECIMAL(15,2) NOT NULL,
  `descripcion` TEXT NULL,
  `codigo_dimensiones` VARCHAR(15) NOT NULL,
  PRIMARY KEY (`codigo_producto`),
  UNIQUE INDEX `codigo_producto_UNIQUE` (`codigo_producto` ASC) VISIBLE,
  UNIQUE INDEX `codigo_gama_UNIQUE` (`codigo_gama` ASC) VISIBLE,
  INDEX `codigo_dimensiones_idx` (`codigo_dimensiones` ASC) VISIBLE,
  CONSTRAINT `codigo_gama`
    FOREIGN KEY (`codigo_gama`)
    REFERENCES `GARDEN_DB`.`gama_producto` (`codigo_gama`)
    ON DELETE NO ACTION
    ON UPDATE NO ACTION,
  CONSTRAINT `codigo_dimensiones`
    FOREIGN KEY (`codigo_dimensiones`)
    REFERENCES `GARDEN_DB`.`dimensiones` (`codigo_dimensiones`)
    ON DELETE NO ACTION
    ON UPDATE NO ACTION)
ENGINE = InnoDB;


-- -----------------------------------------------------
-- Table `GARDEN_DB`.`proveedor`
-- -----------------------------------------------------
CREATE TABLE IF NOT EXISTS `GARDEN_DB`.`proveedor` (
  `codigo_proveedor` INT(11) NOT NULL,
  `nombre` VARCHAR(50) NOT NULL,
  `email` VARCHAR(60) NOT NULL,
  PRIMARY KEY (`codigo_proveedor`),
  UNIQUE INDEX `email_UNIQUE` (`email` ASC) VISIBLE)
ENGINE = InnoDB;


-- -----------------------------------------------------
-- Table `GARDEN_DB`.`producto_proveedor`
-- -----------------------------------------------------
CREATE TABLE IF NOT EXISTS `GARDEN_DB`.`producto_proveedor` (
  `codigo_producto` VARCHAR(15) NOT NULL,
  `codigo_proveedor` INT(11) NOT NULL,
  `precio_proveedor` DECIMAL(15,2) NOT NULL,
  PRIMARY KEY (`codigo_producto`, `codigo_proveedor`),
  INDEX `codigo_producto_idx` (`codigo_producto` ASC) VISIBLE,
  INDEX `codigo_proveedor_idx` (`codigo_proveedor` ASC) VISIBLE,
  CONSTRAINT `FK_cod_producto`
    FOREIGN KEY (`codigo_producto`)
    REFERENCES `GARDEN_DB`.`producto` (`codigo_producto`)
    ON DELETE NO ACTION
    ON UPDATE NO ACTION,
  CONSTRAINT `FK_cod_proveedor`
    FOREIGN KEY (`codigo_proveedor`)
    REFERENCES `GARDEN_DB`.`proveedor` (`codigo_proveedor`)
    ON DELETE NO ACTION
    ON UPDATE NO ACTION)
ENGINE = InnoDB;


-- -----------------------------------------------------
-- Table `GARDEN_DB`.`estado`
-- -----------------------------------------------------
CREATE TABLE IF NOT EXISTS `GARDEN_DB`.`estado` (
  `codigo_estado` INT NOT NULL AUTO_INCREMENT,
  `nombre` VARCHAR(25) NOT NULL,
  PRIMARY KEY (`codigo_estado`))
ENGINE = InnoDB;


-- -----------------------------------------------------
-- Table `GARDEN_DB`.`pais`
-- -----------------------------------------------------
CREATE TABLE IF NOT EXISTS `GARDEN_DB`.`pais` (
  `codigo_pais` VARCHAR(5) NOT NULL,
  `nombre_pais` VARCHAR(50) NOT NULL,
  PRIMARY KEY (`codigo_pais`))
ENGINE = InnoDB;


-- -----------------------------------------------------
-- Table `GARDEN_DB`.`region`
-- -----------------------------------------------------
CREATE TABLE IF NOT EXISTS `GARDEN_DB`.`region` (
  `codigo_region` VARCHAR(10) NOT NULL,
  `nombre_region` VARCHAR(50) NOT NULL,
  `codigo_pais` VARCHAR(5) NOT NULL,
  PRIMARY KEY (`codigo_region`),
  INDEX `codigo_pais_idx` (`codigo_pais` ASC) VISIBLE,
  CONSTRAINT `codigo_pais`
    FOREIGN KEY (`codigo_pais`)
    REFERENCES `GARDEN_DB`.`pais` (`codigo_pais`)
    ON DELETE NO ACTION
    ON UPDATE NO ACTION)
ENGINE = InnoDB;


-- -----------------------------------------------------
-- Table `GARDEN_DB`.`ciudad`
-- -----------------------------------------------------
CREATE TABLE IF NOT EXISTS `GARDEN_DB`.`ciudad` (
  `codigo_ciudad` INT(11) NOT NULL,
  `nombre_ciudad` VARCHAR(50) NOT NULL,
  `codigo_region` VARCHAR(10) NOT NULL,
  PRIMARY KEY (`codigo_ciudad`),
  INDEX `codigo_region_idx` (`codigo_region` ASC) VISIBLE,
  CONSTRAINT `codigo_region`
    FOREIGN KEY (`codigo_region`)
    REFERENCES `GARDEN_DB`.`region` (`codigo_region`)
    ON DELETE NO ACTION
    ON UPDATE NO ACTION)
ENGINE = InnoDB;


-- -----------------------------------------------------
-- Table `GARDEN_DB`.`oficina`
-- -----------------------------------------------------
CREATE TABLE IF NOT EXISTS `GARDEN_DB`.`oficina` (
  `codigo_oficina` INT(11) NOT NULL,
  `codigo_ciudad` INT(11) NOT NULL,
  `codigo_postal` VARCHAR(10) NOT NULL,
  PRIMARY KEY (`codigo_oficina`),
  INDEX `codigo_region_idx` (`codigo_ciudad` ASC) VISIBLE,
  CONSTRAINT `FK_ofi_ciudad`
    FOREIGN KEY (`codigo_ciudad`)
    REFERENCES `GARDEN_DB`.`ciudad` (`codigo_ciudad`)
    ON DELETE NO ACTION
    ON UPDATE NO ACTION)
ENGINE = InnoDB;


-- -----------------------------------------------------
-- Table `GARDEN_DB`.`puesto_empleado`
-- -----------------------------------------------------
CREATE TABLE IF NOT EXISTS `GARDEN_DB`.`puesto_empleado` (
  `codigo_puesto_empleado` INT(11) NOT NULL,
  `nombre_puesto` VARCHAR(45) NOT NULL,
  `extension` VARCHAR(10) NOT NULL,
  PRIMARY KEY (`codigo_puesto_empleado`),
  UNIQUE INDEX `extension_UNIQUE` (`extension` ASC) VISIBLE)
ENGINE = InnoDB;


-- -----------------------------------------------------
-- Table `GARDEN_DB`.`empleado`
-- -----------------------------------------------------
CREATE TABLE IF NOT EXISTS `GARDEN_DB`.`empleado` (
  `codigo_empleado` INT(11) NOT NULL AUTO_INCREMENT,
  `nombre_empleado` VARCHAR(50) NOT NULL,
  `apellido1_empleado` VARCHAR(50) NOT NULL,
  `apellido2_empleado` VARCHAR(50) NULL,
  `email_empleado` VARCHAR(100) NOT NULL,
  `codigo_oficina` INT(11) NOT NULL,
  `codigo_jefe` INT(11) NULL,
  `codigo_puesto_empleado` INT(11) NOT NULL,
  PRIMARY KEY (`codigo_empleado`),
  UNIQUE INDEX `email_empleado_UNIQUE` (`email_empleado` ASC) VISIBLE,
  INDEX `codigo_oficina_idx` (`codigo_oficina` ASC) VISIBLE,
  INDEX `codigo_jefe_idx` (`codigo_jefe` ASC) VISIBLE,
  INDEX `codigo_puesto_empleado_idx` (`codigo_puesto_empleado` ASC) VISIBLE,
  CONSTRAINT `FK_emple_oficina`
    FOREIGN KEY (`codigo_oficina`)
    REFERENCES `GARDEN_DB`.`oficina` (`codigo_oficina`)
    ON DELETE NO ACTION
    ON UPDATE NO ACTION,
  CONSTRAINT `FK_cod_jefe`
    FOREIGN KEY (`codigo_jefe`)
    REFERENCES `GARDEN_DB`.`empleado` (`codigo_empleado`)
    ON DELETE NO ACTION
    ON UPDATE NO ACTION,
  CONSTRAINT `FK_puesto_empleado`
    FOREIGN KEY (`codigo_puesto_empleado`)
    REFERENCES `GARDEN_DB`.`puesto_empleado` (`codigo_puesto_empleado`)
    ON DELETE NO ACTION
    ON UPDATE NO ACTION)
ENGINE = InnoDB;


-- -----------------------------------------------------
-- Table `GARDEN_DB`.`cliente`
-- -----------------------------------------------------
CREATE TABLE IF NOT EXISTS `GARDEN_DB`.`cliente` (
  `codigo_cliente` INT(11) NOT NULL,
  `nombre_cliente` VARCHAR(50) NOT NULL,
  `codigo_ciudad` INT(11) NOT NULL,
  `codigo_postal` VARCHAR(10) NOT NULL,
  `limite_credito` DECIMAL(15,2) NULL,
  `codigo_rep_ventas` INT(11) NOT NULL,
  PRIMARY KEY (`codigo_cliente`),
  INDEX `FK_cod_ciudad_idx` (`codigo_ciudad` ASC) VISIBLE,
  INDEX `FK_cod_rep_ventas_idx` (`codigo_rep_ventas` ASC) VISIBLE,
  CONSTRAINT `FK_cliente_ciudad`
    FOREIGN KEY (`codigo_ciudad`)
    REFERENCES `GARDEN_DB`.`ciudad` (`codigo_ciudad`)
    ON DELETE NO ACTION
    ON UPDATE NO ACTION,
  CONSTRAINT `FK_cod_rep_ventas`
    FOREIGN KEY (`codigo_rep_ventas`)
    REFERENCES `GARDEN_DB`.`empleado` (`codigo_empleado`)
    ON DELETE NO ACTION
    ON UPDATE NO ACTION)
ENGINE = InnoDB;


-- -----------------------------------------------------
-- Table `GARDEN_DB`.`pedido`
-- -----------------------------------------------------
CREATE TABLE IF NOT EXISTS `GARDEN_DB`.`pedido` (
  `codigo_pedido` VARCHAR(15) NOT NULL,
  `fecha_pedido` DATE NOT NULL,
  `fecha_esperada` DATE NOT NULL,
  `fecha_entrega` DATE NULL,
  `comentarios` TEXT NULL,
  `codigo_cliente` INT(11) NOT NULL,
  `codigo_estado` INT NOT NULL,
  PRIMARY KEY (`codigo_pedido`),
  INDEX `codigo_estado_idx` (`codigo_estado` ASC) VISIBLE,
  INDEX `codigo_cliente_idx` (`codigo_cliente` ASC) VISIBLE,
  CONSTRAINT `FK_pedido_estado`
    FOREIGN KEY (`codigo_estado`)
    REFERENCES `GARDEN_DB`.`estado` (`codigo_estado`)
    ON DELETE NO ACTION
    ON UPDATE NO ACTION,
  CONSTRAINT `FK_pedido_cliente`
    FOREIGN KEY (`codigo_cliente`)
    REFERENCES `GARDEN_DB`.`cliente` (`codigo_cliente`)
    ON DELETE NO ACTION
    ON UPDATE NO ACTION)
ENGINE = InnoDB;


-- -----------------------------------------------------
-- Table `GARDEN_DB`.`detalle_pedido`
-- -----------------------------------------------------
CREATE TABLE IF NOT EXISTS `GARDEN_DB`.`detalle_pedido` (
  `codigo_producto` VARCHAR(15) NOT NULL,
  `codigo_pedido` VARCHAR(15) NOT NULL,
  `cantidad` INT(11) NOT NULL,
  `precio_unidad` DECIMAL(15,2) NOT NULL,
  `numero_linea` SMALLINT(6) NOT NULL,
  PRIMARY KEY (`codigo_producto`, `codigo_pedido`),
  INDEX `codigo_pedido_idx` (`codigo_pedido` ASC) VISIBLE,
  CONSTRAINT `FK_det_ped_prod`
    FOREIGN KEY (`codigo_producto`)
    REFERENCES `GARDEN_DB`.`producto` (`codigo_producto`)
    ON DELETE NO ACTION
    ON UPDATE NO ACTION,
  CONSTRAINT `FK_codigo_pedido`
    FOREIGN KEY (`codigo_pedido`)
    REFERENCES `GARDEN_DB`.`pedido` (`codigo_pedido`)
    ON DELETE NO ACTION
    ON UPDATE NO ACTION)
ENGINE = InnoDB;


-- -----------------------------------------------------
-- Table `GARDEN_DB`.`tipo_telefono`
-- -----------------------------------------------------
CREATE TABLE IF NOT EXISTS `GARDEN_DB`.`tipo_telefono` (
  `codigo_tipo_telefono` INT(11) NOT NULL AUTO_INCREMENT,
  `nombre_tipo_telefono` VARCHAR(50) NOT NULL,
  PRIMARY KEY (`codigo_tipo_telefono`))
ENGINE = InnoDB;


-- -----------------------------------------------------
-- Table `GARDEN_DB`.`telefono_cliente`
-- -----------------------------------------------------
CREATE TABLE IF NOT EXISTS `GARDEN_DB`.`telefono_cliente` (
  `codigo_telefono_cliente` INT(11) NOT NULL AUTO_INCREMENT,
  `telefono_cliente` VARCHAR(50) NOT NULL,
  `codigo_cliente` INT(11) NOT NULL,
  `codigo_tipo_telefono` INT(11) NULL,
  PRIMARY KEY (`codigo_telefono_cliente`),
  INDEX `codigo_tipo_telefono_idx` (`codigo_tipo_telefono` ASC) VISIBLE,
  INDEX `codigo_cliente_idx` (`codigo_cliente` ASC) VISIBLE,
  CONSTRAINT `FK_tipo_tel`
    FOREIGN KEY (`codigo_tipo_telefono`)
    REFERENCES `GARDEN_DB`.`tipo_telefono` (`codigo_tipo_telefono`)
    ON DELETE NO ACTION
    ON UPDATE NO ACTION,
  CONSTRAINT `FK_cliente_tel`
    FOREIGN KEY (`codigo_cliente`)
    REFERENCES `GARDEN_DB`.`cliente` (`codigo_cliente`)
    ON DELETE NO ACTION
    ON UPDATE NO ACTION)
ENGINE = InnoDB;


-- -----------------------------------------------------
-- Table `GARDEN_DB`.`tipo_direccion`
-- -----------------------------------------------------
CREATE TABLE IF NOT EXISTS `GARDEN_DB`.`tipo_direccion` (
  `codigo_tipo_dir` SMALLINT NOT NULL AUTO_INCREMENT,
  `nombre_tipo_dir` VARCHAR(50) NOT NULL,
  PRIMARY KEY (`codigo_tipo_dir`))
ENGINE = InnoDB;


-- -----------------------------------------------------
-- Table `GARDEN_DB`.`direccion`
-- -----------------------------------------------------
CREATE TABLE IF NOT EXISTS `GARDEN_DB`.`direccion` (
  `codigo_direccion` INT(11) NOT NULL AUTO_INCREMENT,
  `nombre_calle` VARCHAR(50) NULL,
  `numero_direccion` VARCHAR(50) NULL,
  `nombre_barrio` VARCHAR(50) NULL,
  `codigo_cliente` INT(11) NOT NULL,
  `codigo_tipo_dir` SMALLINT NOT NULL,
  PRIMARY KEY (`codigo_direccion`),
  INDEX `codigo_cliente_idx` (`codigo_cliente` ASC) VISIBLE,
  INDEX `codigo_tipo_dir_idx` (`codigo_tipo_dir` ASC) VISIBLE,
  CONSTRAINT `FK_cliente_dir`
    FOREIGN KEY (`codigo_cliente`)
    REFERENCES `GARDEN_DB`.`cliente` (`codigo_cliente`)
    ON DELETE NO ACTION
    ON UPDATE NO ACTION,
  CONSTRAINT `FK_dir_tipo_dir`
    FOREIGN KEY (`codigo_tipo_dir`)
    REFERENCES `GARDEN_DB`.`tipo_direccion` (`codigo_tipo_dir`)
    ON DELETE NO ACTION
    ON UPDATE NO ACTION)
ENGINE = InnoDB;


-- -----------------------------------------------------
-- Table `GARDEN_DB`.`contacto`
-- -----------------------------------------------------
CREATE TABLE IF NOT EXISTS `GARDEN_DB`.`contacto` (
  `codigo_contacto` VARCHAR(25) NOT NULL,
  `primer_nombre_contacto` VARCHAR(50) NOT NULL,
  `primer_apellido_contacto` VARCHAR(50) NOT NULL,
  `email_contacto` VARCHAR(50) NOT NULL,
  `segundo_apellido_contacto` VARCHAR(50) NULL,
  `segundo_nombre_contacto` VARCHAR(50) NULL,
  `codigo_cliente` INT(11) NOT NULL,
  PRIMARY KEY (`codigo_contacto`),
  UNIQUE INDEX `email_contacto_UNIQUE` (`email_contacto` ASC) VISIBLE,
  INDEX `codigo_cliente_idx` (`codigo_cliente` ASC) VISIBLE,
  CONSTRAINT `FK_contact_cliente`
    FOREIGN KEY (`codigo_cliente`)
    REFERENCES `GARDEN_DB`.`cliente` (`codigo_cliente`)
    ON DELETE NO ACTION
    ON UPDATE NO ACTION)
ENGINE = InnoDB;


-- -----------------------------------------------------
-- Table `GARDEN_DB`.`metodo_pago`
-- -----------------------------------------------------
CREATE TABLE IF NOT EXISTS `GARDEN_DB`.`metodo_pago` (
  `codigo_metodo_pago` INT NOT NULL AUTO_INCREMENT,
  `nombre_met_pago` VARCHAR(50) NOT NULL,
  PRIMARY KEY (`codigo_metodo_pago`))
ENGINE = InnoDB;


-- -----------------------------------------------------
-- Table `GARDEN_DB`.`pago`
-- -----------------------------------------------------
CREATE TABLE IF NOT EXISTS `GARDEN_DB`.`pago` (
  `codigo_pago` INT(11) NOT NULL AUTO_INCREMENT,
  `fecha_pago` DATE NOT NULL,
  `total_pago` DECIMAL(15,2) NOT NULL,
  `codigo_cliente` INT(11) NOT NULL,
  `codigo_met_pago` INT(11) NOT NULL,
  PRIMARY KEY (`codigo_pago`),
  INDEX `codigo_cliente_idx` (`codigo_cliente` ASC) VISIBLE,
  INDEX `codigo_met_pago_idx` (`codigo_met_pago` ASC) VISIBLE,
  CONSTRAINT `FK_pago_cliente`
    FOREIGN KEY (`codigo_cliente`)
    REFERENCES `GARDEN_DB`.`cliente` (`codigo_cliente`)
    ON DELETE NO ACTION
    ON UPDATE NO ACTION,
  CONSTRAINT `FK_cod_met_pago`
    FOREIGN KEY (`codigo_met_pago`)
    REFERENCES `GARDEN_DB`.`metodo_pago` (`codigo_metodo_pago`)
    ON DELETE NO ACTION
    ON UPDATE NO ACTION)
ENGINE = InnoDB;


-- -----------------------------------------------------
-- Table `GARDEN_DB`.`direccion_oficina`
-- -----------------------------------------------------
CREATE TABLE IF NOT EXISTS `GARDEN_DB`.`direccion_oficina` (
  `codigo_direccion` INT(11) NOT NULL AUTO_INCREMENT,
  `nombre_calle` VARCHAR(50) NULL,
  `numero_direccion` VARCHAR(50) NULL,
  `nombre_barrio` VARCHAR(50) NULL,
  `codigo_oficina` INT(10) NOT NULL,
  `codigo_tipo_dir` SMALLINT NOT NULL,
  PRIMARY KEY (`codigo_direccion`),
  INDEX `codigo_oficina_idx` (`codigo_oficina` ASC) VISIBLE,
  INDEX `codigo_tipo_dir_idx` (`codigo_tipo_dir` ASC) VISIBLE,
  CONSTRAINT `FK_dir_oficina`
    FOREIGN KEY (`codigo_oficina`)
    REFERENCES `GARDEN_DB`.`oficina` (`codigo_oficina`)
    ON DELETE NO ACTION
    ON UPDATE NO ACTION,
  CONSTRAINT `FK_tipo_dir_ofi`
    FOREIGN KEY (`codigo_tipo_dir`)
    REFERENCES `GARDEN_DB`.`tipo_direccion` (`codigo_tipo_dir`)
    ON DELETE NO ACTION
    ON UPDATE NO ACTION)
ENGINE = InnoDB;


-- -----------------------------------------------------
-- Table `GARDEN_DB`.`telefono_oficina`
-- -----------------------------------------------------
CREATE TABLE IF NOT EXISTS `GARDEN_DB`.`telefono_oficina` (
  `codigo_telefono_oficina` INT(11) NOT NULL AUTO_INCREMENT,
  `telefono_oficina` VARCHAR(50) NOT NULL,
  `codigo_tipo_telefono` INT(11) NOT NULL,
  `codigo_oficina` INT(11) NOT NULL,
  PRIMARY KEY (`codigo_telefono_oficina`),
  INDEX `codigo_tipo_telefono_idx` (`codigo_tipo_telefono` ASC) VISIBLE,
  INDEX `codigo_oficina_idx` (`codigo_oficina` ASC) VISIBLE,
  CONSTRAINT `FK_tipo_tel_ofi`
    FOREIGN KEY (`codigo_tipo_telefono`)
    REFERENCES `GARDEN_DB`.`tipo_telefono` (`codigo_tipo_telefono`)
    ON DELETE NO ACTION
    ON UPDATE NO ACTION,
  CONSTRAINT `FK_tel_oficina`
    FOREIGN KEY (`codigo_oficina`)
    REFERENCES `GARDEN_DB`.`oficina` (`codigo_oficina`)
    ON DELETE NO ACTION
    ON UPDATE NO ACTION)
ENGINE = InnoDB;


SET SQL_MODE=@OLD_SQL_MODE;
SET FOREIGN_KEY_CHECKS=@OLD_FOREIGN_KEY_CHECKS;
SET UNIQUE_CHECKS=@OLD_UNIQUE_CHECKS;
```

### Inserción de datos

```mysql
-- Inserts para tabla gama_producto
INSERT INTO gama_producto (codigo_gama, gama, descripcion_texto, descripcion_html, imagen) VALUES
(1, 'Flores', 'Productos relacionados con flores', '<p>Productos relacionados con flores</p>', 'flores.jpg'),
(2, 'Plantas', 'Productos relacionados con plantas', '<p>Productos relacionados con plantas</p>', 'plantas.jpg'),
(3, 'Cactus', 'Productos relacionados con cactus', '<p>Productos relacionados con cactus</p>', 'cactus.jpg'),
(4, 'Árboles', 'Productos relacionados con árboles', '<p>Productos relacionados con árboles</p>', 'arboles.jpg'),
(5, 'Flores Artificiales', 'Productos de decoración con flores artificiales', '<p>Productos de decoración con flores artificiales</p>', 'flores_artificiales.jpg'),
(6, 'Plantas de Interior', 'Plantas adecuadas para interiores', '<p>Plantas adecuadas para interiores</p>', 'plantas_interior.jpg'),
(7, 'Macetas', 'Productos para el cultivo de plantas en macetas', '<p>Productos para el cultivo de plantas en macetas</p>', 'macetas.jpg'),
(8, 'Herramientas de Podar', 'Herramientas para podar árboles y arbustos', '<p>Herramientas para podar árboles y arbustos</p>', 'podadoras.jpg'),
(9, 'Fertilizantes', 'Productos para mejorar el crecimiento de las plantas', '<p>Productos para mejorar el crecimiento de las plantas</p>', 'fertilizantes.jpg'),
(10, 'Decoración de Jardín', 'Elementos decorativos para embellecer el jardín', '<p>Elementos decorativos para embellecer el jardín</p>', 'decoracion_jardin.jpg'),
(11, 'Sustratos y Tierras', 'Productos para mejorar la calidad del suelo y el crecimiento de las plantas', '<p>Productos para mejorar la calidad del suelo y el crecimiento de las plantas</p>', 'sustratos.jpg');

-- Inserts para tabla dimensiones
INSERT INTO dimensiones (codigo_dimensiones, largo, alto, ancho, peso) VALUES
('DIM001', '20 cm', '30 cm', '10 cm', '0.5 kg'),
('DIM002', '25 cm', '40 cm', '15 cm', '0.8 kg'),
('DIM003', '10 cm', '15 cm', '10 cm', '0.3 kg'),
('DIM004', '30 cm', '40 cm', '20 cm', '1.2 kg'),
('DIM005', '40 cm', '50 cm', '25 cm', '1.5 kg'),
('DIM006', '20 cm', '25 cm', '20 cm', '0.8 kg'),
('DIM007', '30 cm', '15 cm', '10 cm', '0.4 kg'),
('DIM008', '20 cm', '10 cm', '5 cm', '0.2 kg'),
('DIM009', '40 cm', '20 cm', '15 cm', '0.6 kg');

INSERT INTO proveedor (codigo_proveedor, nombre, email) VALUES
(1, 'Jardinería Flores S.L.', 'info@jardineriaflores.com'),
(2, 'Plantas Mediterráneas S.A.', 'ventas@plantasmediterraneas.com'),
(3, 'Viveros Verdes', 'info@viverosverdes.com'),
(4, 'Herramientas de Jardín y Más', 'ventas@herramientasymas.com'),
(5, 'Plantas y Más', 'info@plantasymas.com'),
(6, 'Decoraciones Jardineras S.L.', 'ventas@decoracionesjardineras.com');

-- Inserts para tabla estado
INSERT INTO estado (nombre) VALUES
('Pendiente'),
('En Proceso'),
('Entregado'),
('En Tránsito'),
('Cancelado'),
('Completado'),
('En Preparación'),
('En Espera'),
('Listo para Envío'),
('Recibido en Almacén'),
('Listo para Retiro');

-- Inserts para tabla tipo_direccion
INSERT INTO tipo_direccion (codigo_tipo_dir, nombre_tipo_dir) VALUES
(1, 'Principal'),
(2, 'Secundaria'),
(3, 'Envío'),
(4, 'Facturación');

-- Inserts para tabla pais
INSERT INTO pais (codigo_pais, nombre_pais) VALUES
('ES', 'España'),
('US', 'Estados Unidos'),
('FR', 'Francia'),
('UK', 'Reino Unido'),
('DE', 'Alemania'),
('JP', 'Japón'),
('IT', 'Italia'),
('AU', 'Australia'),
('BR', 'Brasil'),
('CA', 'Canadá');

-- Inserts para tabla region
INSERT INTO region (codigo_region, nombre_region, codigo_pais) VALUES
('MD', 'Madrid', 'ES'),
('CA', 'California', 'US'),
('IDF', 'Ile-de-France', 'FR'),
('ENG', 'Inglaterra', 'UK'),
('BAV', 'Baviera', 'DE'),
('TKY', 'Tokio', 'JP'),
('ROM', 'Roma', 'IT'),
('LON', 'Londres', 'UK'),
('BER', 'Berlín', 'DE'),
('SYD', 'Sídney', 'AU'),
('RIO', 'Río de Janeiro', 'BR'),
('TOR', 'Toronto', 'CA');

-- Inserts para tabla ciudad
INSERT INTO ciudad (codigo_ciudad, nombre_ciudad, codigo_region) VALUES
(1, 'Madrid', 'MD'),
(2, 'Los Angeles', 'CA'),
(3, 'París', 'IDF'),
(4, 'Londres', 'ENG'),
(5, 'Munich', 'BAV'),
(6, 'Tokio', 'TKY'),
(7, 'Roma', 'ROM'),
(8, 'Sídney', 'SYD'),
(9, 'São Paulo', 'RIO'),
(10, 'Montreal', 'TOR');

-- Inserts para tabla tipo_telefono
INSERT INTO tipo_telefono (codigo_tipo_telefono, nombre_tipo_telefono) VALUES
(1, 'Fijo'),
(2, 'Móvil'),
(3, 'Fax'),
(4, 'Oficina');

-- Inserts para tabla producto
INSERT INTO producto (codigo_producto, nombre, codigo_gama, cantidad_stock, precio_venta, descripcion, codigo_dimensiones) VALUES
('PROD001', 'Rosa Roja', 1, 100, 10.50, 'Hermosa rosa roja para decoración.', 'DIM001'),
('PROD002', 'Hortensia Azul', 2, 80, 15.75, 'Flores de hortensia azul en ramo.', 'DIM002'),
('PROD003', 'Cactus Espinoso', 3, 150, 8.25, 'Cactus pequeño en maceta de cerámica.', 'DIM003'),
('PROD004', 'Maceta de Barro', 4, 150, 5.00, 'Maceta de barro terracota para plantas de interior', 'DIM004'),
('PROD005', 'Tijeras de Podar', 5, 80, 12.00, 'Tijeras profesionales para podar ramas pequeñas', 'DIM005'),
('PROD006', 'Abono Universal', 6, 100, 8.00, 'Abono orgánico para todo tipo de plantas', 'DIM006'),
('PROD007', 'Cactus de Interior', 7, 120, 7.50, 'Pequeños cactus ideales para decorar el interior de la casa', 'DIM007'),
('PROD008', 'Fuente de Jardín', 8, 30, 50.00, 'Fuente de agua con diseño elegante para jardines', 'DIM008'),
('PROD009', 'Tierra para Macetas', 9, 80, 3.00, 'Sustrato universal para macetas de interior y exterior', 'DIM009');

-- Inserts para tabla producto_proveedor
INSERT INTO producto_proveedor (codigo_producto, codigo_proveedor, precio_proveedor) VALUES
('PROD001', 1, 8.00),
('PROD002', 2, 12.50),
('PROD003', 1, 6.50),
('PROD004', 3, 3.50),
('PROD005', 4, 9.00),
('PROD006', 3, 6.50),
('PROD007', 5, 5.50),
('PROD008', 6, 40.00),
('PROD009', 5, 2.50);

-- Inserts para tabla tipo_metodo_pago
INSERT INTO metodo_pago (nombre_met_pago) VALUES
('Tarjeta de Crédito'),
('Transferencia Bancaria'),
('PayPal'),
('Cheque'),
('Efectivo');

INSERT INTO puesto_empleado (codigo_puesto_empleado, nombre_puesto, extension) VALUES
(1, 'Gerente', '100'),
(2, 'Vendedor', '200'),
(3, 'Asistente', '300'),
(4, 'Asistente Administrativo', '400'),
(5, 'Almacenero', '500'),
(6, 'Recepcionista', '600');

-- Inserts para tabla oficina
INSERT INTO oficina (codigo_oficina, codigo_ciudad, codigo_postal) VALUES
(1, 1, '28001'),
(2, 2, '90001'),
(3, 3, '75001'),
(4, 4, 'W1A 1AA'),
(5, 5, '80331'),
(6, 6, '100-0001');

-- Inserts para tabla telefono_oficina
INSERT INTO telefono_oficina (telefono_oficina, codigo_tipo_telefono, codigo_oficina) VALUES
('910123456', 1, 1),
('3109876543', 2, 2),
('3310203040', 1, 3),
('2079460146', 3, 4),
('8912345678', 4, 5),
('0364567890', 3, 6);

-- Inserts para tabla direccion_oficina
INSERT INTO direccion_oficina (nombre_calle, numero_direccion, nombre_barrio, codigo_oficina, codigo_tipo_dir) VALUES
('Calle Principal', '123', 'Centro', 1, 1),
('Avenida Principal', '456', 'Downtown', 2, 1),
('Rue Principale', '789', 'Centre-ville', 3, 1),
('Baker Street', '221b', 'Marylebone', 4, 3),
('Kaufingerstraße', '10', 'Altstadt', 5, 3),
('3 Chome-3-1 Marunouchi', '3', 'Chiyoda City', 6, 3);

-- Inserts para tabla empleado
INSERT INTO empleado (codigo_empleado, nombre_empleado, apellido1_empleado, apellido2_empleado, email_empleado, codigo_oficina, codigo_jefe, codigo_puesto_empleado) VALUES
(1, 'Juan', 'González', 'López', 'juan.gonzalez@empresa.com', 1, NULL, 1),
(2, 'María', 'Martínez', 'Sánchez', 'maria.martinez@empresa.com', 1, 1, 2),
(3, 'Pedro', 'Díaz', 'Pérez', 'pedro.diaz@empresa.com', 2, 1, 2);

-- Inserts para tabla cliente
INSERT INTO cliente (codigo_cliente, nombre_cliente, codigo_ciudad, codigo_postal, limite_credito, codigo_rep_ventas) VALUES
(1, 'Jardines del Sur', 1, '28001', 1500.00, 2),
(2, 'Floristería Rosas Blancas', 2, '41001', 2000.00, 1);

-- Inserts para tabla pedido
INSERT INTO pedido (codigo_pedido, fecha_pedido, fecha_esperada, fecha_entrega, comentarios, codigo_cliente, codigo_estado) VALUES
('PED001', '2024-04-20', '2024-04-25', NULL, 'Por favor entregar antes del fin de semana.', 1, 1),
('PED002', '2024-04-20', '2024-04-23', NULL, 'Pedido urgente.', 2, 2),
('PED003', '2024-04-21', '2024-04-26', NULL, 'Envío a domicilio.', 1, 3),
('PED004', '2024-04-22', '2024-04-27', NULL, 'Entrega en tienda.', 2, 1);

-- Inserts para tabla direccion
INSERT INTO direccion (nombre_calle, numero_direccion, nombre_barrio, codigo_cliente, codigo_tipo_dir) VALUES
('Calle Principal', '123', 'Centro', 1, 1),
('Avenida de las Flores', '456', 'Norte', 2, 1);

-- Inserts para tabla telefono_cliente
INSERT INTO telefono_cliente (telefono_cliente, codigo_cliente, codigo_tipo_telefono) VALUES
('123456789', 1, 1),
('987654321', 2, 1);

-- Inserts para tabla contacto
INSERT INTO contacto (codigo_contacto, primer_nombre_contacto, primer_apellido_contacto, email_contacto, segundo_apellido_contacto, segundo_nombre_contacto, codigo_cliente) VALUES
('CTC001', 'Carlos', 'Fernández', 'carlos.fernandez@cliente.com', 'García', 'José', 1),
('CTC002', 'Ana', 'López', 'ana.lopez@cliente.com', 'Martínez', 'María', 2),
('CTC003', 'James', 'Smith', 'james.smith@cliente.com', 'Brown', 'Robert', 1),
('CTC004', 'Emma', 'Johnson', 'emma.johnson@cliente.com', 'Williams', 'Olivia', 2);

-- Inserts para tabla pago
INSERT INTO pago (fecha_pago, total_pago, codigo_cliente, codigo_met_pago) VALUES
('2024-04-21', 250.00, 1, 1),
('2024-04-22', 350.00, 2, 2),
('2024-04-20', 100.00, 1, 1),
('2024-04-21', 200.00, 2, 2),
('2024-04-22', 300.00, 1, 3),
('2024-04-23', 500.00, 1, 4),
('2024-04-24', 600.00, 2, 5),
('2024-04-25', 700.00, 1, 4);

-- Inserts para tabla detalle_pedido
INSERT INTO detalle_pedido (codigo_producto, codigo_pedido, cantidad, precio_unidad, numero_linea) VALUES
('PROD001', 'PED001', 5, 10.50, 1),
('PROD002', 'PED001', 3, 15.75, 2),
('PROD003', 'PED002', 2, 8.25, 1),
('PROD004', 'PED003', 4, 20.00, 1),
('PROD005', 'PED003', 2, 18.50, 2),
('PROD006', 'PED004', 3, 15.75, 1);
```



1. **Primera Forma Normal (1NF)**:
   - Se crearon tablas separadas para entidades distintas, como producto, proveedor, cliente, empleado, etc. Esto garantiza que cada tabla tenga un formato simple y que contenga solo atributos relevantes para esa entidad.
   - Se aseguró que cada celda de cada tabla contenga un único valor, evitando la redundancia y la repetición de datos.
2. **Segunda Forma Normal (2NF)**:
   - Se identificaron y eliminaron las dependencias parciales en las tablas. Por ejemplo:
     - Se creó la tabla gama_producto para almacenar información sobre las gamas de productos, eliminando la dependencia parcial de la tabla producto con respecto a la descripción de la gama.
     - Se creó la tabla dimensiones para almacenar información sobre las dimensiones de los productos, eliminando la dependencia parcial de la tabla producto con respecto a las dimensiones.
3. **Tercera Forma Normal (3NF)**:
   - Se identificaron y eliminaron las dependencias transitivas en las tablas. Por ejemplo:
     - Se separaron los detalles específicos del proveedor en la tabla proveedor, eliminando la dependencia de la tabla producto con respecto a estos detalles.
     - Se creó la tabla puesto_empleado para almacenar información sobre los puestos de los empleados, eliminando la dependencia de la tabla empleado con respecto a esta información.

### DER

![DER_Garden_DB](https://github.com/Fauroro/taller_3_GardenDB/blob/main/DER_GardenDB.png?raw=true)

### Consultas sobre una tabla

1. Devuelve un listado con el código de oficina y la ciudad donde hay oficinas.

   ```mysql
   SELECT o.codigo_oficina, c.nombre_ciudad
   FROM oficina o
   JOIN ciudad c ON o.codigo_ciudad = c.codigo_ciudad;
   +----------------+---------------+
   | codigo_oficina | nombre_ciudad |
   +----------------+---------------+
   |              1 | Madrid        |
   |              2 | Los Angeles   |
   |              3 | París         |
   |              4 | Londres       |
   |              5 | Munich        |
   |              6 | Tokio         |
   +----------------+---------------+
   6 rows in set (0,04 sec)
   ```

   

2. Devuelve un listado con la ciudad y el teléfono de las oficinas de España.

   ```mysql
   SELECT c.nombre_ciudad, tof.telefono_oficina
   FROM ciudad c
   JOIN region r ON c.codigo_region = r.codigo_region
   JOIN pais p ON r.codigo_pais = p.codigo_pais
   JOIN oficina o ON c.codigo_ciudad = o.codigo_ciudad
   JOIN telefono_oficina tof ON o.codigo_oficina = tof.codigo_oficina
   WHERE p.nombre_pais = 'España';
   +---------------+------------------+
   | nombre_ciudad | telefono_oficina |
   +---------------+------------------+
   | Madrid        | 910123456        |
   +---------------+------------------+
   1 row in set (1,35 sec)
   ```

   

3. Devuelve un listado con el nombre, apellidos y email de los empleados cuyo jefe tiene un código de jefe igual a 7.

   ```mysql
   SELECT e.nombre_empleado, e.apellido1_empleado, e.apellido2_empleado, e.email_empleado
   FROM empleado e
   WHERE e.codigo_jefe = 7;
   Empty set (0,05 sec)
   ```

   

4. Devuelve el nombre del puesto, nombre, apellidos y email del jefe de la empresa.

   ```mysql
   SELECT pe.nombre_puesto, CONCAT(e.nombre_empleado,' ', e.apellido1_empleado,' ', ifnull(e.apellido2_empleado,'')) AS nombre_empleado, e.email_empleado
   FROM empleado e
   JOIN puesto_empleado pe ON e.codigo_puesto_empleado = pe.codigo_puesto_empleado
   WHERE e.codigo_jefe IS NULL;
   +---------------+-----------------------+---------------------------+
   | nombre_puesto | nombre_empleado       | email_empleado            |
   +---------------+-----------------------+---------------------------+
   | Gerente       | Juan González López   | juan.gonzalez@empresa.com |
   +---------------+-----------------------+---------------------------+
   1 row in set (0,01 sec)
   ```

   

5. Devuelve un listado con el nombre, apellidos y puesto de aquellos empleados que no sean representantes de ventas.

   ```mysql
   SELECT e.nombre_empleado, e.apellido1_empleado, e.apellido2_empleado, pe.nombre_puesto
   FROM empleado e
   JOIN puesto_empleado pe ON e.codigo_puesto_empleado = pe.codigo_puesto_empleado
   WHERE pe.nombre_puesto != 'Representante de ventas';
   +-----------------+--------------------+--------------------+---------------+
   | nombre_empleado | apellido1_empleado | apellido2_empleado | nombre_puesto |
   +-----------------+--------------------+--------------------+---------------+
   | Juan            | González           | López              | Gerente       |
   | María           | Martínez           | Sánchez            | Vendedor      |
   | Pedro           | Díaz               | Pérez              | Vendedor      |
   +-----------------+--------------------+--------------------+---------------+
   3 rows in set (0,00 sec)
   ```

   

6. Devuelve un listado con el nombre de los todos los clientes españoles.

   ```mysql
   SELECT cli.nombre_cliente
   FROM cliente cli
   JOIN ciudad c ON cli.codigo_ciudad = c.codigo_ciudad
   JOIN region r ON c.codigo_region = r.codigo_region
   JOIN pais p ON r.codigo_pais = p.codigo_pais
   WHERE p.nombre_pais = 'España';
   +------------------+
   | nombre_cliente   |
   +------------------+
   | Jardines del Sur |
   +------------------+
   1 row in set (0,25 sec)
   ```

   

7. Devuelve un listado con los distintos estados por los que puede pasar un pedido.

   ```mysql
   SELECT nombre
   FROM estado;
   +----------------------+
   | nombre               |
   +----------------------+
   | Pendiente            |
   | En Proceso           |
   | Entregado            |
   | Pendiente            |
   | En Proceso           |
   | Entregado            |
   | En Tránsito          |
   | Cancelado            |
   | Completado           |
   | En Preparación       |
   | En Espera            |
   | Listo para Envío     |
   | En Proceso de Envío  |
   | Recibido en Almacén  |
   | Listo para Retiro    |
   +----------------------+
   15 rows in set (0,23 sec)
   ```

   

8. Devuelve un listado con el código de cliente de aquellos clientes que realizaron algún pago en 2008. Tenga en cuenta que deberá eliminar aquellos códigos de cliente que aparezcan repetidos. Resuelva la consulta:

   * Utilizando la función YEAR de MySQL.

     ```mysql
     SELECT DISTINCT codigo_cliente
     FROM pago
     WHERE YEAR(fecha_pago) = 2008;
     Empty set (0,00 sec)
     ```

     

   * Utilizando la función DATE_FORMAT de MySQL.

     ```mysql
     SELECT DISTINCT codigo_cliente
     FROM pago
     WHERE DATE_FORMAT(fecha_pago, '%Y') = '2008';
     Empty set (0,00 sec)
     ```

     

   * Sin utilizar ninguna de las funciones anteriores.

     ```mysql
     SELECT DISTINCT codigo_cliente
     FROM pago
     WHERE fecha_pago >= '2008-01-01' AND fecha_pago < '2009-01-01';
     Empty set (0,00 sec)
     ```
     
     

9. Devuelve un listado con el código de pedido, código de cliente, fecha esperada y fecha de entrega de los pedidos que no han sido entregados a tiempo.

   ```mysql
   SELECT codigo_pedido, codigo_cliente, fecha_esperada, fecha_entrega
   FROM pedido
   WHERE fecha_entrega > fecha_esperada;
   Empty set (0,31 sec)
   ```

   

10. Devuelve un listado con el código de pedido, código de cliente, fecha esperada y fecha de entrega de los pedidos cuya fecha de entrega ha sido al menos dos días antes de la fecha esperada.

    * Utilizando la función ADDDATE de MySQL.

      ```mysql
      SELECT codigo_pedido, codigo_cliente, fecha_esperada, fecha_entrega
      FROM pedido
      WHERE fecha_entrega < ADDDATE(fecha_esperada, -2);
      Empty set (0,00 sec)
      ```

      

    * Utilizando la función DATEDIFF de MySQL.

      ```mysql
      SELECT codigo_pedido, codigo_cliente, fecha_esperada, fecha_entrega
      FROM pedido
      WHERE DATEDIFF(fecha_esperada, fecha_entrega) >= 2;
      Empty set (0,00 sec)
      ```

      

    * ¿Sería posible resolver esta consulta utilizando el operador de suma + o resta -?

      ```mysql
      SELECT codigo_pedido, codigo_cliente, fecha_esperada, fecha_entrega
      FROM pedido
      WHERE fecha_entrega < fecha_esperada - INTERVAL 2 DAY;
      Empty set (0,00 sec)
      ```
      
      

11. Devuelve un listado de todos los pedidos que fueron rechazados en 2009.

    ```mysql
    SELECT codigo_pedido, fecha_pedido, fecha_entrega, comentarios
    FROM pedido
    WHERE codigo_estado = (
        SELECT codigo_estado
        FROM estado
        WHERE nombre = 'Rechazado'
    ) AND YEAR(fecha_pedido) = 2009;
    Empty set (0,42 sec)
    ```

    

12. Devuelve un listado de todos los pedidos que han sido entregados en el mes de enero de cualquier año.

    ```mysql
    SELECT codigo_pedido, fecha_pedido, fecha_entrega, comentarios
    FROM pedido
    WHERE MONTH(fecha_entrega) = 1;
    Empty set (0,00 sec)
    ```

    

13. Devuelve un listado con todos los pagos que se realizaron en el año 2008 mediante Paypal. Ordene el resultado de mayor a menor.

    ```mysql
    SELECT codigo_pago, fecha_pago, total_pago, codigo_cliente, codigo_met_pago
    FROM pago
    WHERE YEAR(fecha_pago) = 2008 AND codigo_met_pago = (
        SELECT codigo_metodo_pago
        FROM metodo_pago
        WHERE nombre_met_pago = 'Paypal'
    )
    ORDER BY total_pago DESC;
    Empty set (0,00 sec)
    ```

    

14. Devuelve un listado con todas las formas de pago que aparecen en la tabla pago. Tenga en cuenta que no deben aparecer formas de pago repetidas.

    ```mysql
    SELECT DISTINCT mp.codigo_metodo_pago, mp.nombre_met_pago
    FROM pago p
    JOIN metodo_pago mp ON p.codigo_met_pago = mp.codigo_metodo_pago;
    +--------------------+------------------------+
    | codigo_metodo_pago | nombre_met_pago        |
    +--------------------+------------------------+
    |                  1 | Tarjeta de Crédito     |
    |                  2 | Transferencia Bancaria |
    |                  3 | PayPal                 |
    |                  4 | Cheque                 |
    |                  5 | Efectivo               |
    +--------------------+------------------------+
    5 rows in set (0,00 sec)
    ```

    

15. Devuelve un listado con todos los productos que pertenecen a la gama Ornamentales y que tienen más de 100 unidades en stock. El listado deberá estar ordenado por su precio de venta, mostrando en primer lugar los de mayor precio.

    ```mysql
    SELECT p.codigo_producto, p.nombre, p.cantidad_stock, p.precio_venta, p.descripcion, p.codigo_dimensiones
    FROM producto p
    JOIN gama_producto gp ON p.codigo_gama = gp.codigo_gama
    WHERE gp.gama = 'Ornamentales' AND p.cantidad_stock > 100
    ORDER BY p.precio_venta DESC;
    Empty set (0,03 sec)
    ```

    

16. Devuelve un listado con todos los clientes que sean de la ciudad de Madrid y cuyo representante de ventas tenga el código de empleado 11 o 30.

    ```mysql
    SELECT c.codigo_cliente, c.nombre_cliente, c.codigo_ciudad, c.codigo_postal, c.limite_credito, c.codigo_rep_ventas
    FROM cliente c
    JOIN ciudad ci ON c.codigo_ciudad = ci.codigo_ciudad
    JOIN empleado e ON c.codigo_rep_ventas = e.codigo_empleado
    WHERE ci.nombre_ciudad = 'Madrid' AND e.codigo_empleado IN (11, 30);
    Empty set (0,00 sec)
    ```

    

### Consultas multitabla (Composición interna)

Resuelva todas las consultas utilizando la sintaxis de SQL1 y SQL2. Las consultas con sintaxis de SQL2 se deben resolver con INNER JOIN y NATURAL JOIN.

1. Obtén un listado con el nombre de cada cliente y el nombre y apellido de su representante de ventas.

   ```mysql
   SELECT c.nombre_cliente, CONCAT(e.nombre_empleado,' ', e.apellido1_empleado,' ', ifnull(e.apellido2_empleado,'')) AS representante_ventas
   FROM cliente AS c
   INNER JOIN empleado AS e ON c.codigo_rep_ventas = e.codigo_empleado;
   +----------------------------+---------------------------+
   | nombre_cliente             | representante_ventas      |
   +----------------------------+---------------------------+
   | Jardines del Sur           | María Martínez Sánchez    |
   | Floristería Rosas Blancas  | Juan González López       |
   +----------------------------+---------------------------+
   2 rows in set (0,00 sec)
   ```

   

2. Muestra el nombre de los clientes que hayan realizado pagos junto con el nombre de sus representantes de ventas.

   ```mysql
   SELECT DISTINCT c.nombre_cliente, CONCAT(e.nombre_empleado,' ', e.apellido1_empleado,' ', ifnull(e.apellido2_empleado,'')) AS representante_ventas
   FROM cliente AS c
   INNER JOIN empleado AS e ON c.codigo_rep_ventas = e.codigo_empleado
   INNER JOIN pago AS p ON c.codigo_cliente = p.codigo_cliente;
   +----------------------------+---------------------------+
   | nombre_cliente             | representante_ventas      |
   +----------------------------+---------------------------+
   | Jardines del Sur           | María Martínez Sánchez    |
   | Floristería Rosas Blancas  | Juan González López       |
   +----------------------------+---------------------------+
   2 rows in set (0,00 sec)
   ```

   

3. Muestra el nombre de los clientes que no hayan realizado pagos junto con el nombre de sus representantes de ventas.

   ```mysql
   SELECT c.nombre_cliente, CONCAT(e.nombre_empleado,' ', e.apellido1_empleado,' ', ifnull(e.apellido2_empleado,'')) AS representante_ventas
   FROM cliente AS c
   INNER JOIN empleado AS e ON c.codigo_rep_ventas = e.codigo_empleado
   LEFT JOIN pago AS p ON c.codigo_cliente = p.codigo_cliente
   WHERE p.codigo_pago IS NULL;
   Empty set (0,00 sec)
   ```

   

4. Devuelve el nombre de los clientes que han hecho pagos y el nombre de sus representantes junto con la ciudad de la oficina a la que pertenece el representante.

   ```mysql
   SELECT DISTINCT cli.nombre_cliente, CONCAT(e.nombre_empleado,' ', e.apellido1_empleado,' ', ifnull(e.apellido2_empleado,'')) AS representante_ventas, c.nombre_ciudad
   FROM cliente AS cli
   INNER JOIN empleado as e ON cli.codigo_rep_ventas = e.codigo_empleado
   INNER JOIN pago AS p ON cli.codigo_cliente = p.codigo_cliente
   INNER JOIN oficina AS o ON e.codigo_oficina = o.codigo_oficina
   INNER JOIN ciudad as c ON o.codigo_ciudad = c.codigo_ciudad;
   +----------------------------+---------------------------+---------------+
   | nombre_cliente             | representante_ventas      | nombre_ciudad |
   +----------------------------+---------------------------+---------------+
   | Jardines del Sur           | María Martínez Sánchez    | Madrid        |
   | Floristería Rosas Blancas  | Juan González López       | Madrid        |
   +----------------------------+---------------------------+---------------+
   2 rows in set (0,00 sec)
   ```

   

5. Devuelve el nombre de los clientes que no hayan hecho pagos y el nombre de sus representantes junto con la ciudad de la oficina a la que pertenece el representante.

   ```mysql
   SELECT c.nombre_cliente, CONCAT(e.nombre_empleado,' ', e.apellido1_empleado,' ', ifnull(e.apellido2_empleado,'')) AS representante_ventas, ci.nombre_ciudad
   FROM cliente AS c
   INNER JOIN empleado as e ON c.codigo_rep_ventas = e.codigo_empleado
   LEFT JOIN pago as p ON c.codigo_cliente = p.codigo_cliente
   INNER JOIN oficina as o ON e.codigo_oficina = o.codigo_oficina
   INNER JOIN ciudad AS ci ON o.codigo_ciudad = ci.codigo_ciudad
   WHERE p.codigo_pago IS NULL;
   Empty set (0,00 sec)
   ```

   

6. Lista la dirección de las oficinas que tengan clientes en Fuenlabrada.

   ```mysql
   SELECT d.nombre_calle, d.numero_direccion, d.nombre_barrio
   FROM direccion AS d
   INNER JOIN oficina as o ON o.codigo_oficina = o.codigo_oficina
   INNER JOIN ciudad as c ON o.codigo_ciudad = c.codigo_ciudad
   INNER JOIN cliente AS cli ON cli.codigo_ciudad = c.codigo_ciudad
   WHERE c.nombre_ciudad = 'Fuenlabrada';
   Empty set (0,01 sec)
   
   ```

   

7. Devuelve el nombre de los clientes y el nombre de sus representantes junto con la ciudad de la oficina a la que pertenece el representante.

   ```mysql
   SELECT c.nombre_cliente, CONCAT(e.nombre_empleado,' ', e.apellido1_empleado,' ', ifnull(e.apellido2_empleado,'')) AS representante_ventas, ci.nombre_ciudad
   FROM cliente c
   INNER JOIN empleado e ON c.codigo_rep_ventas = e.codigo_empleado
   INNER JOIN oficina o ON e.codigo_oficina = o.codigo_oficina
   INNER JOIN ciudad ci ON o.codigo_ciudad = ci.codigo_ciudad;
   +----------------------------+---------------------------+---------------+
   | nombre_cliente             | representante_ventas      | nombre_ciudad |
   +----------------------------+---------------------------+---------------+
   | Jardines del Sur           | María Martínez Sánchez    | Madrid        |
   | Floristería Rosas Blancas  | Juan González López       | Madrid        |
   +----------------------------+---------------------------+---------------+
   2 rows in set (0,00 sec)
   ```

   

8. Devuelve un listado con el nombre de los empleados junto con el nombre de sus jefes.

   ```mysql
   SELECT CONCAT(e.nombre_empleado,' ', e.apellido1_empleado,' ', ifnull(e.apellido2_empleado,'')) AS empleado, CONCAT(j.nombre_empleado,' ', j.apellido1_empleado,' ', ifnull(j.apellido2_empleado,'')) AS nombre_jefe
   FROM empleado e
   INNER JOIN empleado j ON e.codigo_jefe = j.codigo_empleado;
   +---------------------------+-----------------------+
   | empleado                  | nombre_jefe           |
   +---------------------------+-----------------------+
   | María Martínez Sánchez    | Juan González López   |
   | Pedro Díaz Pérez          | Juan González López   |
   +---------------------------+-----------------------+
   2 rows in set (0,00 sec)
   ```

   

9. Devuelve un listado que muestre el nombre de cada empleados, el nombre de su jefe y el nombre del jefe de sus jefe.

   ```mysql
   SELECT CONCAT(e.nombre_empleado,' ', e.apellido1_empleado,' ', ifnull(e.apellido2_empleado,'')) AS empleado, CONCAT(j1.nombre_empleado,' ', j1.apellido1_empleado,' ', ifnull(j1.apellido2_empleado,'')) AS nombre_jefe, CONCAT(j2.nombre_empleado,' ', j2.apellido1_empleado,' ', ifnull(j2.apellido2_empleado,'')) AS jefe_del_jefe
   FROM empleado e
   INNER JOIN empleado j1 ON e.codigo_jefe = j1.codigo_empleado
   INNER JOIN empleado j2 ON j1.codigo_jefe = j2.codigo_empleado;
   Empty set (0,00 sec)
   ```

   

10. Devuelve el nombre de los clientes a los que no se les ha entregado a tiempo un pedido.

    ```mysql
    SELECT c.nombre_cliente
    FROM cliente c
    INNER JOIN pedido p ON c.codigo_cliente = p.codigo_cliente
    WHERE p.fecha_entrega > p.fecha_esperada;
    Empty set (0,00 sec)
    ```

    

11. Devuelve un listado de las diferentes gamas de producto que ha comprado cada cliente.

    ```mysql
    SELECT DISTINCT c.nombre_cliente, gp.gama
    FROM cliente c
    INNER JOIN pedido pe ON c.codigo_cliente = pe.codigo_cliente
    INNER JOIN detalle_pedido dp ON pe.codigo_pedido = dp.codigo_pedido
    INNER JOIN producto p ON dp.codigo_producto = p.codigo_producto
    INNER JOIN gama_producto gp ON p.codigo_gama = gp.codigo_gama;
    +----------------------------+---------+
    | nombre_cliente             | gama    |
    +----------------------------+---------+
    | Jardines del Sur           | Flores  |
    | Jardines del Sur           | Plantas |
    | Floristería Rosas Blancas  | Cactus  |
    +----------------------------+---------+
    3 rows in set (4,35 sec)
    ```

    

### Consultas multitabla (Composición externa)

Resuelva todas las consultas utilizando las cláusulas LEFT JOIN, RIGHT JOIN, NATURAL LEFT JOIN y NATURAL RIGHT JOIN.

1. Devuelve un listado que muestre solamente los clientes que no han realizado ningún pago.

   ```mysql
   SELECT c.codigo_cliente, c.nombre_cliente
   FROM cliente AS c
   LEFT JOIN pago AS p ON c.codigo_cliente = p.codigo_cliente
   WHERE p.codigo_pago IS NULL;
   Empty set (0,00 sec)
   ```

   

2. Devuelve un listado que muestre solamente los clientes que no han realizado ningún pedido.

   ```mysql
   SELECT c.codigo_cliente, c.nombre_cliente
   FROM cliente AS c
   NATURAL LEFT JOIN pedido AS p
   WHERE p.codigo_pedido IS NULL;
   Empty set (0,00 sec)
   ```

   

3. Devuelve un listado que muestre los clientes que no han realizado ningún pago y los que no han realizado ningún pedido.

   ```mysql
   SELECT c.codigo_cliente, c.nombre_cliente
   FROM cliente AS c
   NATURAL LEFT JOIN pago AS p
   WHERE p.codigo_pago IS NULL
   UNION
   SELECT c.codigo_cliente, c.nombre_cliente
   FROM cliente AS c
   NATURAL LEFT JOIN pedido
   WHERE pedido.codigo_pedido IS NULL;
   Empty set (0,02 sec)
   ```

   

4. Devuelve un listado que muestre solamente los empleados que no tienen una oficina asociada.

   ```mysql
   SELECT e.codigo_empleado, CONCAT(e.nombre_empleado,' ', e.apellido1_empleado,' ', ifnull(e.apellido2_empleado,'')) AS nombre_empleado, e.email_empleado, e.codigo_oficina, e.codigo_jefe, e.codigo_puesto_empleado
   FROM empleado e
   NATURAL LEFT JOIN oficina o
   WHERE o.codigo_oficina IS NULL;
   Empty set (0,00 sec)
   ```

   

5. Devuelve un listado que muestre solamente los empleados que no tienen un cliente asociado.

   ```mysql
   SELECT e.codigo_empleado, CONCAT(e.nombre_empleado,' ', e.apellido1_empleado,' ', ifnull(e.apellido2_empleado,'')) AS nombre_empleado, e.email_empleado, e.codigo_oficina, e.codigo_jefe, e.codigo_puesto_empleado
   FROM empleado e
   NATURAL LEFT JOIN cliente c
   WHERE c.codigo_cliente IS NULL;
   Empty set (0,00 sec)
   ```

   

6. Devuelve un listado que muestre solamente los empleados que no tienen un cliente asociado junto con los datos de la oficina donde trabajan.

   ```mysql
   SELECT e.codigo_empleado, CONCAT(e.nombre_empleado,' ', e.apellido1_empleado,' ', ifnull(e.apellido2_empleado,'')) AS nombre_empleado, e.email_empleado, e.codigo_oficina, e.codigo_jefe, e.codigo_puesto_empleado
   FROM empleado e
   NATURAL LEFT JOIN cliente c
   LEFT JOIN oficina o ON e.codigo_oficina = o.codigo_oficina
   WHERE c.codigo_cliente IS NULL;
   Empty set (0,00 sec)
   ```

   

7. Devuelve un listado que muestre los empleados que no tienen una oficina asociada y los que no tienen un cliente asociado.

   ```mysql
   SELECT e.codigo_empleado, CONCAT(e.nombre_empleado,' ', e.apellido1_empleado,' ', ifnull(e.apellido2_empleado,'')) AS nombre_empleado, e.email_empleado, e.codigo_oficina, e.codigo_jefe, e.codigo_puesto_empleado
   FROM empleado e
   LEFT JOIN cliente c ON e.codigo_empleado = c.codigo_rep_ventas
   WHERE c.codigo_cliente IS NULL;
   +-----------------+--------------------+------------------------+----------------+-------------+------------------------+
   | codigo_empleado | nombre_empleado    | email_empleado         | codigo_oficina | codigo_jefe | codigo_puesto_empleado |
   +-----------------+--------------------+------------------------+----------------+-------------+------------------------+
   |               3 | Pedro Díaz Pérez   | pedro.diaz@empresa.com |              2 |           1 |                      2 |
   +-----------------+--------------------+------------------------+----------------+-------------+------------------------+
   1 row in set (0,05 sec)
   ```

   

8. Devuelve un listado de los productos que nunca han aparecido en un pedido.

   ```mysql
   SELECT p.codigo_producto, p.nombre, p.codigo_gama, p.cantidad_stock, p.precio_venta, p.codigo_dimensiones
   FROM producto p
   LEFT JOIN detalle_pedido dp ON p.codigo_producto = dp.codigo_producto
   WHERE dp.codigo_producto IS NULL;
   +-----------------+---------------------+-------------+----------------+--------------+--------------------+
   | codigo_producto | nombre              | codigo_gama | cantidad_stock | precio_venta | codigo_dimensiones |
   +-----------------+---------------------+-------------+----------------+--------------+--------------------+
   | PROD004         | Maceta de Barro     |           4 |            150 |         5.00 | DIM004             |
   | PROD005         | Tijeras de Podar    |           5 |             80 |        12.00 | DIM005             |
   | PROD006         | Abono Universal     |           6 |            100 |         8.00 | DIM006             |
   | PROD007         | Cactus de Interior  |           7 |            120 |         7.50 | DIM007             |
   | PROD008         | Fuente de Jardín    |           8 |             30 |        50.00 | DIM008             |
   | PROD009         | Tierra para Macetas |           9 |             80 |         3.00 | DIM009             |
   +-----------------+---------------------+-------------+----------------+--------------+--------------------+
   6 rows in set (0,00 sec)
   
   ```

   

9. Devuelve un listado de los productos que nunca han aparecido en un pedido. El resultado debe mostrar el nombre, la descripción y la imagen del producto.

   ```mysql
   
   ```

   

10. Devuelve las oficinas donde no trabajan ninguno de los empleados que hayan sido los representantes de ventas de algún cliente que haya realizado la compra de algún producto de la gama Frutales.

    ```mysql
    SELECT o.codigo_oficina, o.codigo_ciudad, o.codigo_postal
    FROM oficina o
    LEFT JOIN empleado e ON o.codigo_oficina = e.codigo_oficina
    LEFT JOIN cliente c ON e.codigo_empleado = c.codigo_rep_ventas
    LEFT JOIN pedido ped ON c.codigo_cliente = ped.codigo_cliente
    LEFT JOIN detalle_pedido dp ON ped.codigo_pedido = dp.codigo_pedido
    LEFT JOIN producto prod ON dp.codigo_producto = prod.codigo_producto
    LEFT JOIN gama_producto gp ON prod.codigo_gama = gp.codigo_gama
    WHERE gp.gama = 'Frutales'
    GROUP BY o.codigo_oficina
    HAVING COUNT(e.codigo_empleado) = 0;
    Empty set (0,00 sec)
    ```

    

11. Devuelve un listado con los clientes que han realizado algún pedido pero no han realizado ningún pago.

    ```mysql
    SELECT codigo_cliente, nombre_cliente, codigo_ciudad, codigo_postal, limite_credito, codigo_rep_ventas
    FROM cliente
    WHERE codigo_cliente IN (
        SELECT DISTINCT codigo_cliente
        FROM pedido
        WHERE codigo_cliente NOT IN (
            SELECT DISTINCT codigo_cliente
            FROM pago
        )
    );
    Empty set (0,01 sec)
    ```

    

12. Devuelve un listado con los datos de los empleados que no tienen clientes asociados y el nombre de su jefe asociado.

    ```mysql
    SELECT CONCAT(e.nombre_empleado,' ', e.apellido1_empleado,' ', ifnull(e.apellido2_empleado,'')) AS nombre_empleado, e.email_empleado, e.codigo_jefe, CONCAT(j.nombre_empleado,' ', j.apellido1_empleado,' ', ifnull(j.apellido2_empleado,'')) AS nombre_jefe
    FROM empleado e
    LEFT JOIN cliente c ON e.codigo_empleado = c.codigo_rep_ventas
    LEFT JOIN empleado j ON e.codigo_jefe = j.codigo_empleado
    WHERE c.codigo_rep_ventas IS NULL;
    +--------------------+------------------------+-------------+-----------------------+
    | nombre_empleado    | email_empleado         | codigo_jefe | nombre_jefe           |
    +--------------------+------------------------+-------------+-----------------------+
    | Pedro Díaz Pérez   | pedro.diaz@empresa.com |           1 | Juan González López   |
    +--------------------+------------------------+-------------+-----------------------+
    1 row in set (0,01 sec)
    ```

    

### Consultas resumen

1. ¿Cuántos empleados hay en la compañía?

   ```mysql
   SELECT COUNT(codigo_empleado) AS total_empleados
   FROM empleado;
   +-----------------+
   | total_empleados |
   +-----------------+
   |               3 |
   +-----------------+
   1 row in set (0,14 sec)
   ```

   

2. ¿Cuántos clientes tiene cada país?

   ```mysql
   SELECT p.nombre_pais, COUNT(c.codigo_cliente) AS total_clientes
   FROM cliente c
   JOIN ciudad ci ON c.codigo_ciudad = ci.codigo_ciudad
   JOIN region r ON ci.codigo_region = r.codigo_region
   JOIN pais p ON r.codigo_pais = p.codigo_pais
   GROUP BY p.nombre_pais;
   +----------------+----------------+
   | nombre_pais    | total_clientes |
   +----------------+----------------+
   | España         |              1 |
   | Estados Unidos |              1 |
   +----------------+----------------+
   2 rows in set (0,00 sec)
   ```

   

3. ¿Cuál fue el pago medio en 2009?

   ```mysql
   SELECT AVG(total_pago) AS pago_medio_2009
   FROM pago
   WHERE YEAR(fecha_pago) = 2009;
   +-----------------+
   | pago_medio_2009 |
   +-----------------+
   |            NULL |
   +-----------------+
   1 row in set (0,00 sec)
   ```

   

4. ¿Cuántos pedidos hay en cada estado? Ordena el resultado de forma descendente por el número de pedidos.

   ```mysql
   SELECT e.nombre AS nombre_estado, COUNT(p.codigo_pedido) AS total_pedidos
   FROM pedido p
   JOIN estado e ON p.codigo_estado = e.codigo_estado
   GROUP BY p.codigo_estado, nombre_estado
   ORDER BY total_pedidos DESC;
   +---------------+---------------+
   | nombre_estado | total_pedidos |
   +---------------+---------------+
   | Pendiente     |             1 |
   | En Proceso    |             1 |
   +---------------+---------------+
   2 rows in set (0,00 sec)
   ```

   

5. Calcula el precio de venta del producto más caro y más barato en una misma consulta.

   ```mysql
   SELECT MAX(precio_venta) AS precio_mas_caro, MIN(precio_venta) AS precio_mas_barato
   FROM producto;
   +-----------------+-------------------+
   | precio_mas_caro | precio_mas_barato |
   +-----------------+-------------------+
   |           50.00 |              3.00 |
   +-----------------+-------------------+
   1 row in set (0,00 sec)
   ```

   

6. Calcula el número de clientes que tiene la empresa.

   ```mysql
   SELECT COUNT(codigo_cliente) AS total_clientes
   FROM cliente;
   +----------------+
   | total_clientes |
   +----------------+
   |              2 |
   +----------------+
   1 row in set (0,32 sec)
   ```

   

7. ¿Cuántos clientes existen con domicilio en la ciudad de Madrid?

   ```mysql
   SELECT COUNT(codigo_cliente) AS total_clientes_madrid
   FROM cliente c
   JOIN ciudad ci ON c.codigo_ciudad = ci.codigo_ciudad
   WHERE ci.nombre_ciudad = 'Madrid';
   +-----------------------+
   | total_clientes_madrid |
   +-----------------------+
   |                     1 |
   +-----------------------+
   1 row in set (0,00 sec)
   ```

   

8. ¿Calcula cuántos clientes tiene cada una de las ciudades que empiezan por M?

   ```mysql
   SELECT ci.nombre_ciudad, COUNT(c.codigo_cliente) AS total_clientes
   FROM cliente c
   JOIN ciudad ci ON c.codigo_ciudad = ci.codigo_ciudad
   WHERE ci.nombre_ciudad LIKE 'M%'
   GROUP BY ci.nombre_ciudad;
   +---------------+----------------+
   | nombre_ciudad | total_clientes |
   +---------------+----------------+
   | Madrid        |              1 |
   +---------------+----------------+
   1 row in set (0,00 sec)
   ```

   

9. Devuelve el nombre de los representantes de ventas y el número de clientes al que atiende cada uno.

   ```mysql
   SELECT CONCAT(e.nombre_empleado,' ', e.apellido1_empleado,' ', ifnull(e.apellido2_empleado,'')) AS representante_ventas, COUNT(c.codigo_cliente) AS clientes_atendidos
   FROM empleado e
   JOIN cliente c ON e.codigo_empleado = c.codigo_rep_ventas
   GROUP BY representante_ventas;
   +-----------------+--------------------+
   +---------------------------+--------------------+
   | representante_ventas      | clientes_atendidos |
   +---------------------------+--------------------+
   | Juan González López       |                  1 |
   | María Martínez Sánchez    |                  1 |
   +---------------------------+--------------------+
   2 rows in set (0,00 sec)
   ```

   

10. Calcula el número de clientes que no tiene asignado representante de ventas.

    ```mysql
    SELECT COUNT(codigo_cliente) AS clientes_sin_representante
    FROM cliente
    WHERE codigo_rep_ventas IS NULL OR codigo_rep_ventas = '';
    +----------------------------+
    | clientes_sin_representante |
    +----------------------------+
    |                          0 |
    +----------------------------+
    1 row in set (0,00 sec)
    ```

    

11. Calcula la fecha del primer y último pago realizado por cada uno de los clientes. El listado deberá mostrar el nombre y los apellidos de cada cliente.

    ```mysql
    SELECT c.nombre_cliente, CONCAT(co.primer_nombre_contacto,' ', ifnull(co.segundo_nombre_contacto,''),' ',  co.primer_apellido_contacto,' ', ifnull(co.segundo_apellido_contacto,'')) AS nombre_contacto, MIN(p.fecha_pago) AS primera_fecha_pago, MAX(p.fecha_pago) AS ultima_fecha_pago
    FROM cliente c
    LEFT JOIN contacto co ON c.codigo_cliente = co.codigo_cliente
    LEFT JOIN pago p ON c.codigo_cliente = p.codigo_cliente
    GROUP BY c.nombre_cliente, nombre_contacto;
    
    +----------------------------+---------------------------------+--------------------+-------------------+
    | nombre_cliente             | nombre_contacto                 | primera_fecha_pago | ultima_fecha_pago |
    +----------------------------+---------------------------------+--------------------+-------------------+
    | Jardines del Sur           | James Robert Smith Brown        | 2024-04-20         | 2024-04-25        |
    | Jardines del Sur           | Carlos José Fernández García    | 2024-04-20         | 2024-04-25        |
    | Floristería Rosas Blancas  | Emma Olivia Johnson Williams    | 2024-04-21         | 2024-04-24        |
    | Floristería Rosas Blancas  | Ana María López Martínez        | 2024-04-21         | 2024-04-24        |
    +----------------------------+---------------------------------+--------------------+-------------------+
    4 rows in set (0,00 sec)
    ```

    

12. Calcula el número de productos diferentes que hay en cada uno de los pedidos.

    ```mysql
    SELECT codigo_pedido, COUNT(DISTINCT codigo_producto) AS num_productos_diferentes
    FROM detalle_pedido
    GROUP BY codigo_pedido;
    +---------------+--------------------------+
    | codigo_pedido | num_productos_diferentes |
    +---------------+--------------------------+
    | PED001        |                        2 |
    | PED002        |                        1 |
    +---------------+--------------------------+
    2 rows in set (0,00 sec)
    ```

    

13. Calcula la suma de la cantidad total de todos los productos que aparecen en cada uno de los pedidos.

    ```mysql
    SELECT codigo_pedido, SUM(cantidad) AS cantidad_total
    FROM detalle_pedido
    GROUP BY codigo_pedido;
    +---------------+----------------+
    | codigo_pedido | cantidad_total |
    +---------------+----------------+
    | PED001        |              8 |
    | PED002        |              2 |
    +---------------+----------------+
    2 rows in set (0,00 sec)
    ```

    

14. Devuelve un listado de los 20 productos más vendidos y el número total de unidades que se han vendido de cada uno. El listado deberá estar ordenado por el número total de unidades vendidas.

    ```mysql
    SELECT dp.codigo_producto, p.nombre, SUM(dp.cantidad) AS total_unidades_vendidas
    FROM detalle_pedido dp
    JOIN producto p ON dp.codigo_producto = p.codigo_producto
    GROUP BY dp.codigo_producto, p.nombre
    ORDER BY total_unidades_vendidas DESC
    LIMIT 20;
    +-----------------+-----------------+-------------------------+
    | codigo_producto | nombre          | total_unidades_vendidas |
    +-----------------+-----------------+-------------------------+
    | PROD001         | Rosa Roja       |                       5 |
    | PROD002         | Hortensia Azul  |                       3 |
    | PROD003         | Cactus Espinoso |                       2 |
    +-----------------+-----------------+-------------------------+
    3 rows in set (0,00 sec)
    ```

    

15. La facturación que ha tenido la empresa en toda la historia, indicando la base imponible, el IVA y el total facturado. La base imponible se calcula sumando el coste del producto por el número de unidades vendidas de la tabla detalle_pedido. El IVA es el 21 % de la base imponible, y el total la suma de los dos campos anteriores.

    ```mysql
    SELECT 
        SUM(dp.precio_unidad * dp.cantidad) AS base_imponible,
        SUM(dp.precio_unidad * dp.cantidad) * 0.21 AS iva,
        SUM(dp.precio_unidad * dp.cantidad) * 1.21 AS total_facturado
    FROM detalle_pedido dp;
    +----------------+---------+-----------------+
    | base_imponible | iva     | total_facturado |
    +----------------+---------+-----------------+
    |         116.25 | 24.4125 |        140.6625 |
    +----------------+---------+-----------------+
    1 row in set (0,00 sec)
    
    ```

    

16. La misma información que en la pregunta anterior, pero agrupada por código de producto.

    ```mysql
    SELECT 
        dp.codigo_producto,
        SUM(dp.precio_unidad * dp.cantidad) AS base_imponible,
        SUM(dp.precio_unidad * dp.cantidad) * 0.21 AS iva,
        SUM(dp.precio_unidad * dp.cantidad) * 1.21 AS total_facturado
    FROM detalle_pedido dp
    GROUP BY dp.codigo_producto;
    +-----------------+----------------+---------+-----------------+
    | codigo_producto | base_imponible | iva     | total_facturado |
    +-----------------+----------------+---------+-----------------+
    | PROD001         |          52.50 | 11.0250 |         63.5250 |
    | PROD002         |          47.25 |  9.9225 |         57.1725 |
    | PROD003         |          16.50 |  3.4650 |         19.9650 |
    +-----------------+----------------+---------+-----------------+
    3 rows in set (0,00 sec)
    ```

    

17. La misma información que en la pregunta anterior, pero agrupada por código de producto filtrada por los códigos que empiecen por OR.

    ```mysql
    SELECT 
        dp.codigo_producto,
        SUM(dp.precio_unidad * dp.cantidad) AS base_imponible,
        SUM(dp.precio_unidad * dp.cantidad) * 0.21 AS iva,
        SUM(dp.precio_unidad * dp.cantidad) * 1.21 AS total_facturado
    FROM detalle_pedido dp
    JOIN producto p ON dp.codigo_producto = p.codigo_producto
    WHERE p.codigo_producto LIKE 'OR%'
    GROUP BY dp.codigo_producto;
    Empty set (0,00 sec)
    
    ```

    

18. Lista las ventas totales de los productos que hayan facturado más de 3000 euros. Se mostrará el nombre, unidades vendidas, total facturado y total facturado con impuestos (21% IVA).

    ```mysql
    SELECT 
        p.nombre AS nombre_producto,
        SUM(dp.cantidad) AS unidades_vendidas,
        SUM(dp.precio_unidad * dp.cantidad) AS total_facturado,
        SUM(dp.precio_unidad * dp.cantidad) * 1.21 AS total_facturado_con_IVA
    FROM detalle_pedido dp
    JOIN producto p ON dp.codigo_producto = p.codigo_producto
    GROUP BY p.nombre
    HAVING total_facturado > 3000;
    Empty set (0,00 sec)
    ```

    

19. Muestre la suma total de todos los pagos que se realizaron para cada uno de los años que aparecen en la tabla pagos.

    ```mysql
    
    ```

    

### Consultas variadas 

1. Devuelve el listado de clientes indicando el nombre del cliente y cuántos pedidos ha realizado. Tenga en cuenta que pueden existir clientes que no han realizado ningún pedido.

     ```mysql
     SELECT c.nombre_cliente,COUNT(p.codigo_pedido) AS total_pedidos
     FROM cliente c
     LEFT JOIN pedido p ON c.codigo_cliente = p.codigo_cliente
     GROUP BY c.codigo_cliente;
     +----------------------------+---------------+
     | nombre_cliente             | total_pedidos |
     +----------------------------+---------------+
     | Jardines del Sur           |             1 |
     | Floristería Rosas Blancas  |             1 |
     +----------------------------+---------------+
     2 rows in set (0,00 sec)
     ```

     

2. Devuelve un listado con los nombres de los clientes y el total pagado por cada uno de ellos. Tenga en cuenta que pueden existir clientes que no han realizado ningún pago.

     ```mysql
     SELECT c.nombre_cliente, COALESCE(SUM(p.total_pago), 0) AS total_pagado
     FROM cliente c
     LEFT JOIN pago p ON c.codigo_cliente = p.codigo_cliente
     GROUP BY c.codigo_cliente;
     +----------------------------+--------------+
     | nombre_cliente             | total_pagado |
     +----------------------------+--------------+
     | Jardines del Sur           |      2100.00 |
     | Floristería Rosas Blancas  |      1500.00 |
     +----------------------------+--------------+
     2 rows in set (0,00 sec)
     ```

     

3. Devuelve el nombre de los clientes que hayan hecho pedidos en 2008 ordenados alfabéticamente de menor a mayor.

     ```mysql
     SELECT DISTINCT c.nombre_cliente
     FROM cliente c
     INNER JOIN pedido p ON c.codigo_cliente = p.codigo_cliente
     WHERE YEAR(p.fecha_pedido) = 2008
     ORDER BY c.nombre_cliente ASC;
     Empty set (0,00 sec)
     ```

     

4. Devuelve el nombre del cliente, el nombre y primer apellido de su representante de ventas y el número de teléfono de la oficina del representante de ventas, de aquellos clientes que no hayan realizado ningún pago.

     ```mysql
     SELECT c.nombre_cliente, e.nombre_empleado, e.apellido1_empleado, t.telefono_oficina
     FROM cliente c
     LEFT JOIN empleado e ON c.codigo_rep_ventas = e.codigo_empleado
     LEFT JOIN telefono_oficina t ON e.codigo_oficina = t.codigo_oficina
     LEFT JOIN pago p ON c.codigo_cliente = p.codigo_cliente
     WHERE p.codigo_pago IS NULL;
     Empty set (0,02 sec)
     ```

     

5. Devuelve el listado de clientes donde aparezca el nombre del cliente, el nombre y primer apellido de su representante de ventas y la ciudad donde está su oficina.

     ```mysql
     SELECT c.nombre_cliente, CONCAT(e.nombre_empleado,' ', e.apellido1_empleado) AS representante_ventas, ci.nombre_ciudad AS ciudad_oficina
     FROM cliente c
     JOIN empleado e ON c.codigo_rep_ventas = e.codigo_empleado
     JOIN oficina o ON e.codigo_oficina = o.codigo_oficina
     JOIN ciudad ci ON o.codigo_ciudad = ci.codigo_ciudad;
     +----------------------------+----------------------+----------------+
     | nombre_cliente             | representante_ventas | ciudad_oficina |
     +----------------------------+----------------------+----------------+
     | Jardines del Sur           | María Martínez       | Madrid         |
     | Floristería Rosas Blancas  | Juan González        | Madrid         |
     +----------------------------+----------------------+----------------+
     2 rows in set (0,00 sec)
     ```

     

6. Devuelve el nombre, apellidos, puesto y teléfono de la oficina de aquellos empleados que no sean representante de ventas de ningún cliente.

     ```mysql
     SELECT CONCAT(e.nombre_empleado,' ', e.apellido1_empleado,' ', ifnull(e.apellido2_empleado,'')) AS no_rep_ventas, pu.nombre_puesto AS puesto, t.telefono_oficina AS telefono_oficina
     FROM empleado e
     JOIN puesto_empleado pu ON e.codigo_puesto_empleado = pu.codigo_puesto_empleado
     LEFT JOIN cliente c ON e.codigo_empleado = c.codigo_rep_ventas
     LEFT JOIN oficina o ON e.codigo_oficina = o.codigo_oficina
     LEFT JOIN telefono_oficina t ON o.codigo_oficina = t.codigo_oficina
     WHERE c.codigo_cliente IS NULL;
     +--------------------+----------+------------------+
     | no_rep_ventas      | puesto   | telefono_oficina |
     +--------------------+----------+------------------+
     | Pedro Díaz Pérez   | Vendedor | 3109876543       |
     +--------------------+----------+------------------+
     1 row in set (0,00 sec)
     ```

     

7. Devuelve un listado indicando todas las ciudades donde hay oficinas y el número de empleados que tiene.

     ```mysql
     SELECT c.nombre_ciudad AS ciudad, COUNT(*) AS numero_empleados
     FROM ciudad c
     JOIN oficina o ON c.codigo_ciudad = o.codigo_ciudad
     JOIN empleado e ON o.codigo_oficina = e.codigo_oficina
     GROUP BY c.nombre_ciudad;
     +-------------+------------------+
     | ciudad      | numero_empleados |
     +-------------+------------------+
     | Madrid      |                2 |
     | Los Angeles |                1 |
     +-------------+------------------+
     2 rows in set (0,00 sec)
     ```
     
     
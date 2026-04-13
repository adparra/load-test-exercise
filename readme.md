# PRUEBA DE CARGA - LOGIN SERVICE 

## Instrucciones de ejecución

### TECNOLOGÍAS REQUERIDAS

- **Apache JMeter**: 5.6.3
- **Java**: 11 o superior (requerido por JMeter 5.6.3)

## ESTRUCTURA DEL REPOSITORIO

```
load-test-exercise/ 
├── jmeter/
│   └── PruebadecargaLogin.jmx 
├── data/
│   └── users.csv 
├── results/ 
├── readme.md
└── conclusiones.txt
```

## CONSIDERACIONES PREVIAS (leer antes de ejecutar)

### RUTAS RELATIVAS

El JMX usa rutas relativas al directorio donde está ubicado el propio archivo .jmx (`jmeter/`). No es necesario modificar ninguna ruta al clonar el repositorio, siempre que se respete la estructura:

- **CSV**: `../data/users.csv`
- **Reportes**: 
  - `../results/summary-report.csv`
  - `../results/aggregate-report.csv`

Si se cambia la estructura de carpetas, actualizar los campos "Filename" correspondientes en JMeter.

### ASSERTION ERRORS EN EL REPORTE HTML

Para que el reporte HTML muestre el detalle de qué assertion falló en cada petición, agregar las siguientes dos líneas al final del archivo `apache-jmeter-5.6.3/bin/user.properties`:

```properties
jmeter.save.saveservice.assertions=true
jmeter.save.saveservice.assertion_results_failure_message=true
```

Guardar el archivo y reiniciar JMeter para que el cambio aplique.

## PASOS DE EJECUCIÓN

1. Verificar que Java 11+ esté instalado:
   ```bash
   java -version
   ```

2. Clonar el repositorio:
   ```bash
   git clone https://github.com/adparra/load-test-exercise.git
   cd load-test-exercise
   ```

3. Aplicar el cambio en `user.properties` (ver Consideraciones, punto 2) y reiniciar JMeter si estaba abierto.

4. Limpiar resultados anteriores (omitir en la primera ejecución):
   ```bash
   rm -f results/results.jtl 
   rm -rf results/html-report
   rm jmeter.log
   ```

5. Ejecutar la prueba en modo CLI (recomendado):
   
   **Linux/Mac:**
   ```bash
   jmeter -n -t jmeter/PruebadecargaLogin.jmx -l results/results.jtl -e -o results/html-report
   ```
   
   **Windows:**
   ```bash
   jmeter -n -t jmeter\PruebadecargaLogin.jmx -l results\results.jtl -e -o results\html-report
   ```

6. Al finalizar, abrir el reporte en el navegador:
   ```
   results/html-report/index.html
   ```

## VISUALIZACIÓN DEL REPORTE HTML

Una vez abierto `index.html`, las secciones relevantes son:

- **Statistics**: Throughput (TPS), tiempos de respuesta (p95), porcentaje de error por sampler.
- **Errors**: Detalle de errores por tipo. Si se aplicó el cambio en `user.properties`, aquí se verá qué assertion específica falló y cuántas veces.
- **Response Times (en la pestaña charts)**: Gráfica de tiempos de respuesta a lo largo de la prueba, útil para detectar degradación.
- **Throughput (en la pestaña charts)**: Gráfica de TPS por segundo, permite ver el comportamiento durante el ramp-up vs. la fase sostenida.

## CRITERIOS DE ACEPTACIÓN

| Métrica | Criterio |
|---------|----------|
| Throughput | >= 20 TPS (columna "Throughput" en Statistics) |
| Tiempo de respuesta | p95 <= 1,500 ms |
| Tasa de error | < 3% |

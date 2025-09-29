markdown
# Ejemplo de pruebas de regresión con Selenium y Node.js

Este ejemplo demuestra cómo configurar un proyecto de Node.js para realizar pruebas de regresión automatizadas utilizando `selenium-webdriver` junto con el framework de pruebas **Mocha**.

## 1. Preparación del proyecto

1.  **Crea una carpeta** para tu proyecto y navega hasta ella en la terminal:
    ```bash
    mkdir selenium-test-regresion
    cd selenium-test-regresion
    ```

2.  **Inicializa un proyecto de Node.js**:
    ```bash
    npm init -y
    ```

3.  **Instala las dependencias necesarias**:
    -   `selenium-webdriver`: La librería principal de Selenium.
    -   `mocha`: Un framework de pruebas para Node.js.
    -   `chai`: Una librería de aserciones que se integra bien con Mocha.
    -   `chromedriver`: El driver para controlar el navegador Chrome.

    ```bash
    npm install selenium-webdriver mocha chai chromedriver --save-dev
    ```

4.  **Configura el script de pruebas** en tu archivo `package.json` para que Mocha ejecute tus tests.
    ```json
    "scripts": {
      "test": "mocha tests/**/*.spec.js"
    }
    ```

## 2. Creación del test de regresión

Este ejemplo simula una prueba de regresión para una funcionalidad de búsqueda en Google.

1.  **Crea la estructura de carpetas**:
    ```bash
    mkdir tests
    ```

2.  **Crea el archivo de prueba** `tests/busqueda.spec.js` con el siguiente contenido:

    **`tests/busqueda.spec.js`**
    ```javascript
    const { Builder, By, Key, until } = require('selenium-webdriver');
    const { expect } = require('chai');

    // Describe un conjunto de pruebas para una funcionalidad
    describe('Pruebas de regresión de búsqueda', function() {
        this.timeout(30000); // Aumenta el tiempo de espera por si el navegador tarda

        let driver;

        // Antes de cada prueba, inicializa el navegador
        beforeEach(async function() {
            driver = await new Builder().forBrowser('chrome').build();
        });

        // Después de cada prueba, cierra el navegador
        afterEach(async function() {
            await driver.quit();
        });

        // Caso de prueba específico
        it('Debería buscar "Selenium" y mostrar resultados correctos', async function() {
            try {
                // 1. Navegar a la página web (Google)
                await driver.get('https://www.google.com');

                // 2. Encontrar el campo de búsqueda por su nombre ('q')
                const searchBox = await driver.findElement(By.name('q'));

                // 3. Escribir el término de búsqueda y presionar Enter
                await searchBox.sendKeys('Selenium', Key.RETURN);

                // 4. Esperar hasta que el título de la página cambie
                await driver.wait(until.titleContains('Selenium'), 5000);

                // 5. Verificar que el título de la página contiene el término de búsqueda
                const pageTitle = await driver.getTitle();
                expect(pageTitle).to.include('Selenium');

            } catch (error) {
                console.error('Error durante la ejecución del test:', error);
                throw error; // Propagar el error para que la prueba falle
            }
        });
    });
    ```

## 3. Ejecución del test

Ejecuta las pruebas desde la terminal:
```bash
npm test
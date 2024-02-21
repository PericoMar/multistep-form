# Formulario Multipasos con Validación de Campos y Funcionalidades Adicionales

Este ejercicio consiste en desarrollar un formulario multipasos con validación de campos en el que se recolectan los siguientes datos: Nombre, Apellidos, Email, Contraseña (confirmación de contraseña), Teléfono y Dirección. Además, se incluyen funcionalidades adicionales como botones para mostrar/ocultar las contraseñas con una barra de seguridad para calcular la complejidad de la contraseña, integración con la API Places de Google Maps para autocompletar la dirección y cambio de divisas.

## Desarrollo de la Aplicación

El formulario se divide en varios pasos, cada uno con un conjunto específico de campos y validaciones. A medida que el usuario completa cada paso, se habilita el siguiente hasta que se completan todos los pasos y se muestra un mensaje de confirmación.

### Pasos del Formulario

## Pasos del Formulario

1. **Paso 1: Datos Personales**
    - Nombre
    - Apellidos

2. **Paso 2: Información de Contacto**
    - Email
    - Contraseña (con confirmación y medición de fuerza)
    En la siguiente función podemos ver como la fuerza de la contraseña depende del uso de mayusculas, de que sea de más de 8 caracteres, de uso de numeros y caracteres especiales.
    Una vez calculada la fuerza de la contraseña se actualiza la barra de medición de fuerza.
    ```
    function calculatePasswordStrength(password) {
        let strength = 0;
        const REGEX_MAYUS = /[A-ZÁÉIOU]/;
        const REGEX_NUM = /[0-9]/;
        const REGEX_SPECIAL_CARACTER = /[-!$%^&*()_+|~=`{}\[\]:\";'<>?,.\/@#]/;
        if (password.length > 8) {
            strength += 25;
        }
        if (REGEX_MAYUS.test(password)) {
            strength += 25;
        }
        if (REGEX_NUM.test(password)) {
            strength += 25;
        }
        if (REGEX_SPECIAL_CARACTER.test(password)) {
            strength += 25;
        }
        return strength;
    }


    function updateStrengthMeter(strength) {
        // Actualizar el ancho de la barra de progreso y el color
        strengthBar.style = `width: ${strength}%`
        if (strength <= 25) {
            strengthBar.classList.remove('bg-success', 'bg-warning');
            strengthBar.classList.add('bg-danger');
        } else if (strength <= 75) {
            strengthBar.classList.remove('bg-success', 'bg-danger');
            strengthBar.classList.add('bg-warning');
        } else {
            strengthBar.classList.remove('bg-warning', 'bg-danger');
            strengthBar.classList.add('bg-success');
        }
    }

    ```
    - Teléfono

3. **Paso 3: Género y Fecha de Nacimiento**
    - Género
    - Fecha de Nacimiento

4. **Paso 4: Dirección**
    - País
    - Calle (con integración de la API Places de Google Maps)

    ```
    window.initAutocomplete = function () {
            autocomplete = new google.maps.places.Autocomplete(
                document.getElementById("input-autocomplete"),
                {
                    types: ['address'],
                    componentRestrictions: { country: paises[pais].iso },
                    fields: ['formatted_address']
                });

            autocomplete.addListener('place_changed', onPlaceChanged);
        };

        function onPlaceChanged() {
            let place = autocomplete.getPlace();

            if (!place.geometry) {
                inputAutocomplete.placeholder = 'Calle Violeta Parra';
            } else {
                details = place.name;
            }
        }
    ```

5. **Paso 5: Cambio de Divisas**
    - Cantidad a convertir
    - Divisa base
    - Divisa de destino

## Submit del formulario
Cuando se completa el formulario se muestra un modal con la información del cambio de divisas y permite la posibilidad de enviar por correo la información.
```
    formulario.addEventListener("submit", (e) => {
        e.preventDefault();
        fetch(`https://v6.exchangerate-api.com/v6/${API_KEY_CURRENCY}/latest/${baseCurrency}`)
        .then(response => response.json())
        .then(result => {
            convertedAmount = result.conversion_rates[currency] * amount;
            thanksModule.innerHTML = `<article>
                                        <h2>¡Gracias por tu solicitud, ${nom} ${ape}!</h2>
                                        <p>Has cambiado ${amount} ${baseCurrency} a ${currency} con éxito. El monto convertido es ${convertedAmount}.</p>
                                        <p>Te agradecemos por utilizar nuestro servicio.</p>
                                    </article>`;
            const btnCerrarModal = document.createElement("button");
            const anchorMail = document.createElement("a");
            anchorMail.href = `mailto:${email}?subject=Exchange&body=Gracias%20por%20la%20solicitud.%0A%0AHas%20cambiado%20${amount}%20${baseCurrency}%20a%20${currency}%20con%20éxito.%20El%20monto%20convertido%20es%20${convertedAmount}.%0A%0A(Este%20correo%20no%20puede%20ser%20respondido)`;
            anchorMail.innerHTML = "Enviar la información por correo";
            btnCerrarModal.innerHTML = "Cerrar";
            btnCerrarModal.classList.add("button-cerrar-modal")
            thanksModule.appendChild(btnCerrarModal);
            thanksModule.appendChild(anchorMail);
            btnCerrarModal.addEventListener("click", closeModal);
            thanksModule.showModal();
        });
    });
```
Como podemos ver en el codigo anterior antes de mostrar el modal hacemos la petición a la API [exchangerate-api](https://www.exchangerate-api.com/) para conocer cuanto es el cambio:
```
    fetch(`https://v6.exchangerate-api.com/v6/${API_KEY_CURRENCY}/latest/${baseCurrency}`)
```

### Funcionalidades Adicionales

- **Validación de Campos:** Se implementa una validación en tiempo real de los campos del formulario para garantizar que se introduzcan datos válidos.
- **Mostrar/Ocultar Contraseña:** Se incluyen botones para mostrar u ocultar la contraseña, junto con una barra de seguridad que muestra la complejidad de la contraseña.
- **Integración con API Places de Google Maps:** Para la dirección, se utiliza la API Places de Google Maps para autocompletar la dirección y mostrar sugerencias mientras el usuario escribe.
- **Cambio de Divisas:** Se incluye la funcionalidad de cambio de divisas, permitiendo al usuario seleccionar su país y calcular el cambio de moneda.

## Tecnologías Utilizadas

- HTML, CSS y JavaScript para la estructura, estilo y funcionalidad del formulario.
- API Places de Google Maps para autocompletar la dirección y proporcionar sugerencias de ubicación.
- Otras API para calcular el cambio de divisas.
- Bootstrap para el diseño y la responsividad del formulario.

Al completar todos los pasos del formulario, se muestra un modal de agradecimiento con un mensaje personalizado y la información del cambio de divisas. Esta aplicación proporciona una experiencia interactiva y completa para la recopilación de datos del usuario.

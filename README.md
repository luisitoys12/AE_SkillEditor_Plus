# AE Skill Editor Plus — Editor de Habilidades para Unity

> Fork traducido al español por [@luisitoys12](https://github.com/luisitoys12)  
> Proyecto original: [ancientElement/AE_SkillEditor_Plus](https://github.com/ancientElement/AE_SkillEditor_Plus)

---

## ⚠️ ADVERTENCIA IMPORTANTE

**Después de agregar una pista (track) o un Clip, NO modifiques el nombre de las clases.**  
Si cambias el nombre de una clase, **todos los activos (assets) que contengan esa pista o Clip quedarán inválidos e inutilizables.**

---

## 🎬 Video de Explicación

https://www.bilibili.com/video/BV1LD421s7Dn/?spm_id_from=333.788

---

## Crear un Activo (Asset)

Haz clic derecho en el panel de Proyecto → **Create → AETimeline编辑器 → AETimelineAssets** para crear un activo.

![](ImagesAssets/Pasted%20image%2020240503172205.png)

---

## Abrir la Ventana del Editor

Ve al menú **Tools → AETimeline编辑器** para abrir la ventana del editor.

![](ImagesAssets/Pasted%20image%2020240503172328.png)

---

## Seleccionar un Activo

En el editor:
- **Campo izquierdo (ObjectField):** el objeto al que está adjunto el AETimelineAssets.
- **Campo derecho:** selecciona el activo AETimelineAssets que quieres editar.

![](ImagesAssets/Pasted%20image%2020240503172433.png)

---

## Edición

### Clic Derecho — Crear y Eliminar

Mediante el clic derecho puedes crear pistas (tracks) y Clips.

**Crear una pista:** Haz clic derecho en el área vacía del **encabezado de la pista** → aparece un menú contextual → selecciona el tipo de pista a crear.

![](ImagesAssets/Pasted%20image%2020240503172754.png)

**Eliminar una pista:** Haz clic derecho en el **encabezado de la pista** → selecciona eliminar en el menú contextual.

![](ImagesAssets/Pasted%20image%2020240503172849.png)

**Agregar un Clip:** Haz clic derecho en el **área vacía del cuerpo de la pista** → agrega un nuevo Clip a esa pista.

![](ImagesAssets/Pasted%20image%2020240503173002.png)

---

### Atajos de Teclado para Clips

| Atajo | Acción |
|---|---|
| `Ctrl + C` | Copiar el Clip seleccionado |
| `Ctrl + V` | Pegar el Clip copiado en la posición del cursor |
| `Ctrl + X` | Cortar el Clip seleccionado |
| `Delete` | Eliminar el Clip seleccionado |

---

### Manipular Clips con el Mouse

- **Mover:** Arrastra el Clip para cambiar su posición de inicio.
- **Redimensionar:** Coloca el cursor al **final del Clip** — cuando aparezca el ícono de redimensionar (Resize), arrastra para cambiar la duración del Clip.

### Rueda del Mouse (Botón Central)

Con el **botón central del mouse** sobre la zona derecha de la pista, puedes **desplazarte horizontalmente** por la línea de tiempo.

---

## Pistas Personalizadas

*(AEAnimationTrack)*

Para crear pistas personalizadas:

- Las **pistas** deben heredar de `StandardTrack`.
- Los **Clips** deben heredar de `StandardClip`.
- Usa el atributo `AEBindClip` para vincular ambas clases.

Cualquier clase que herede de `StandardTrack` se agregará automáticamente al menú contextual de creación de pistas.

> **Nota importante:** Al escribir la clase de un Clip, esta **debe estar en un archivo separado** y el **nombre del archivo debe coincidir exactamente con el nombre de la clase.**

![](ImagesAssets/Pasted%20image%2020240503190400.png)

### Atributos Obligatorios

Al crear pistas personalizadas, los siguientes atributos son **obligatorios**:

| Atributo | Descripción |
|---|---|
| `AETrackName` | Define el nombre visible de la pista |
| `AETrackColor` | Define el color de la pista |
| `AEBindClip` | Vincula el tipo de Clip correspondiente a la pista |
| `Serializable` | Marca la clase como serializable |

![](ImagesAssets/Pasted%20image%2020240503173908.png)

---

### Estilos Personalizados con AEClipStyleAttribute

*(AEAnimationTrack, CustomAnimationClip)*

Cuando necesitas personalizar el aspecto visual (estilo) de un Clip en el editor, usa el atributo `AEClipStyleAttribute`. Este atributo indica qué clase contiene la función de estilo personalizado del Clip.

- **`ClassName`:** Debe ser el nombre completo del tipo (`Type.FullName`).
- **`OverrideUI`:** Si es `true`, reemplaza la UI original. Si es `false`, dibuja encima de la UI original.

![](ImagesAssets/Pasted%20image%2020240503190945.png)

![](ImagesAssets/Pasted%20image%2020240503170103.png)

**Ejemplo:** `AEAnimationTrack` escribe su función de estilo personalizado dentro de `CustomAnimationClip`. Por lo tanto, se pasa `AE_SkillEditor_Plus.Excample.BuiltTracks.CustomAnimationClip` como `ClassName`.

> **Importante:**
> - La función de estilo personalizado debe ser **estática**.
> - Sus parámetros deben ser **exactamente iguales** a los de `CustomAnimationClip.UpdateUI`.
> - Si no se cumple esto, el sistema reportará un error y la función no tendrá efecto.

![](ImagesAssets/Pasted%20image%2020240503191332.png)

Las clases de estilo personalizado **deben colocarse dentro de una carpeta `Editor`**.

![](ImagesAssets/Pasted%20image%2020240503173825.png)

---

### Comportamiento de Pistas (Track Behaviour)

*(AEAnimationEditorBehaviour)*

![](ImagesAssets/Pasted%20image%2020240503173908.png)

Para que una pista tenga comportamiento, la clase `Track` debe implementar las interfaces `IEditorBehaviour` e `IRuntimeBehaviour`, y los métodos:

- `CreateEditorBehaviour()` → retorna un `AEPlayableBehaviour` para el editor.
- `CreateRuntimeBehaviour()` → retorna un `AEPlayableBehaviour` para en tiempo de ejecución.

#### AEPlayableBehaviour

![](ImagesAssets/Pasted%20image%2020240503195339.png)

`AEPlayableBehaviour` expone los siguientes métodos de ciclo de vida para personalizar el comportamiento de un Clip:

| Método | Descripción |
|---|---|
| `OnEnter` | Se llama al iniciar el Clip |
| `Tick` | Se llama cada frame mientras el Clip está activo |
| `OnExit` | Se llama al terminar el Clip |

> **Nota:** Al sobreescribir `OnEnter` u `OnExit` en una subclase, **siempre llama al método base** (`base.OnEnter()` / `base.OnExit()`).

En el **constructor** del Behaviour, obtén y guarda la referencia al Clip correspondiente. Así podrás acceder a sus datos desde los métodos del ciclo de vida.

**Ejemplo:** En `AEAnimationEditorBehaviour`, el constructor obtiene el `AEAnimationClip`. Luego, en `Tick`, se llama a `SampleAnimation` cada frame para reproducir la animación cuadro a cuadro.

---

## Modo Runtime (Tiempo de Ejecución)

![](ImagesAssets/Pasted%20image%2020240503173908.png)

Para ejecutar pistas en **tiempo de ejecución**, la clase `Track` **debe implementar la interfaz `IRuntimeBehaviour`**. La implementación de `AEPlayableBehaviour` es la misma descrita anteriormente.

![](ImagesAssets/Pasted%20image%2020240503174758.png)

`AETimelineTick` es el **controlador de ejecución en tiempo real**. Para reproducir un activo:

1. Llama a `AETimelineTick.PlayAsset(asset)` pasando el activo que quieres reproducir.
2. Llama a la **actualización del controlador dentro del método `Update`** de tu script.

---

## Pistas Integradas (Built-in)

### Pista de Animación

![](ImagesAssets/Pasted%20image%2020240503185358.png)

En la pista de animación, cuando un Clip excede el rango original de la animación:
- Se muestra una **línea divisoria roja** indicando el punto final de la animación.
- Cuando el Clip encaja exactamente, la línea divisoria es **verde**.

Esto se implementa usando estilos Clip personalizados (`AEClipStyleAttribute`).

Para que el desplazamiento de personajes funcione correctamente con múltiples segmentos de animación en modo editor, se usa el campo `StartPosition` para registrar la posición inicial de cada Clip.

![](ImagesAssets/Pasted%20image%2020240503191938.png)

**Cómo copiar la posición al final de la animación anterior:**
1. Reproduce hasta el final de la animación anterior.
2. Selecciona la propiedad `Position` del personaje y presiona `Ctrl+C`.
3. Ve al panel del Clip y selecciona el campo `StartPosition`.
4. Presiona `Ctrl+V` para pegar la posición copiada.

![](ImagesAssets/Pasted%20image%2020240503192154.png)

---

## Pistas de Efectos y Sonido

Las **pistas de efectos visuales (VFX)** y **pistas de audio (SFX)** son sencillas de usar, no requieren configuración especial.

> - Si el audio **no tiene volumen**, revisa que el parámetro `Volume` **no sea 0**.
> - En la pista de efectos, si activas el parámetro **`Follow`**, el efecto visual **mantendrá su posición relativa** respecto al `GameObject` al que está adjunto.

---

*Traducido al español por [@luisitoys12](https://github.com/luisitoys12) — fork del proyecto original [ancientElement/AE_SkillEditor_Plus](https://github.com/ancientElement/AE_SkillEditor_Plus)*

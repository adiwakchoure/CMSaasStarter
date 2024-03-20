<script lang="ts">
  let canvas: HTMLCanvasElement
  let ctx: CanvasRenderingContext2D | null = null
  let writingMode = false
  let signature: string | null = null

  const formFields = [
    {
      id: "name",
      label: "Name *",
      inputType: "text",
      autocomplete: "given-name",
    },
    {
      id: "email",
      label: "Email *",
      inputType: "email",
      autocomplete: "email",
    },
    {
      id: "phone",
      label: "Phone Number",
      inputType: "tel",
      autocomplete: "tel",
    },
    {
      id: "message",
      label: "Message",
      inputType: "textarea",
      autocomplete: "off",
    },
  ]

  const handleSubmit = () => {
    const formData = {
      firstName: (document.getElementById("first_name") as HTMLInputElement)
        ?.value,
      lastName: (document.getElementById("last_name") as HTMLInputElement)
        ?.value,
      email: (document.getElementById("email") as HTMLInputElement)?.value,
      phone: (document.getElementById("phone") as HTMLInputElement)?.value,
      company: (document.getElementById("company") as HTMLInputElement)?.value,
      message: (document.getElementById("message") as HTMLTextAreaElement)
        ?.value,
      signature: signature,
    }

    // Send the form data to your server or perform any desired action
    console.log(formData)
  }

  const handlePointerDown = (event: PointerEvent) => {
    if (!ctx) return
    writingMode = true
    ctx.beginPath()
    const [positionX, positionY] = getCursorPosition(event)
    ctx.moveTo(positionX, positionY)
  }

  const handlePointerUp = () => {
    writingMode = false
  }

  const handlePointerMove = (event: PointerEvent) => {
    if (!writingMode || !ctx) return
    const [positionX, positionY] = getCursorPosition(event)
    ctx.lineTo(positionX, positionY)
    ctx.stroke()
  }

  const getCursorPosition = (event: PointerEvent) => {
    const rect = canvas.getBoundingClientRect()
    const positionX = event.clientX - rect.x
    const positionY = event.clientY - rect.y
    return [positionX, positionY]
  }

  const clearPad = () => {
    if (!ctx) return
    ctx.clearRect(0, 0, canvas.width, canvas.height)
    signature = null
  }

  $: if (canvas && !ctx) {
    ctx = canvas.getContext("2d")
    ctx.lineWidth = 1 // Adjust the line width as desired
    ctx.lineJoin = ctx.lineCap = "round"
  }
</script>

<div class="flex justify-center items-center min-h-screen">
  <div class="w-full max-w-md p-6">
    <h1 class="text-3xl font-serif mb-6 text-center text-gray-700">
      Write me a letter
    </h1>
    <form class="space-y-4" on:submit|preventDefault={handleSubmit}>
      {#each formFields as field}
        <div>
          <label for={field.id} class="block font-serif mb-1 text-gray-700">
            {field.label}
          </label>
          {#if field.inputType === "textarea"}
            <textarea
              id={field.id}
              name={field.id}
              autocomplete={field.autocomplete}
              rows={4}
              class="w-full p-2 font-serif text-gray-700 bg-gray-200 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500"
              required={field.label.includes("*")}
            />
          {:else}
            <input
              id={field.id}
              name={field.id}
              type={field.inputType}
              autocomplete={field.autocomplete}
              class="w-full p-2 font-serif text-gray-700 bg-gray-200 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500"
              required={field.label.includes("*")}
            />
          {/if}
        </div>
      {/each}

      <div>
        <label for="signature" class="block font-serif mb-1 text-gray-700"
          >Signature</label
        >
        <div class="relative">
          <canvas
            height="150"
            width="400%"
            bind:this={canvas}
            on:pointerdown={handlePointerDown}
            on:pointerup={handlePointerUp}
            on:pointermove={handlePointerMove}
            class="rounded-md bg-gray-200 focus:outline-none focus:ring-2 focus:ring-blue-500"
          />
          <button
            type="button"
            class="absolute right-2 top-2 text-gray-500 hover:text-gray-700 font-serif"
            on:click|preventDefault={clearPad}
            disabled={!signature}
          >
            Clear
          </button>
        </div>
      </div>

      <button
        type="submit"
        class="w-full bg-blue-500 text-white font-serif py-2 rounded-md hover:bg-blue-600 transition-colors duration-300 focus:outline-none focus:ring-2 focus:ring-blue-500"
        disabled={!signature}
      >
        Submit
      </button>
    </form>
  </div>
</div>

<style>
  button:disabled {
    opacity: 0.5;
    cursor: not-allowed;
  }
</style>

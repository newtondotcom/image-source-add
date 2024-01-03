<script lang="ts">
import { onMount } from "svelte";

let width = 300;
let height = 200;
let url: string;

function getSource(url:string) {
  const source = url.split('/')[2];
  return source;
}

function checkCorrecURl(url:string){
  const regex = new RegExp('^(https?:\\/\\/)?'+ // protocol
  '((([a-z\\d]([a-z\\d-]*[a-z\\d])*)\\.)+[a-z]{2,}|'+ // domain name
  '((\\d{1,3}\\.){3}\\d{1,3}))'+ // OR ip (v4) address
  '(\\:\\d+)?(\\/[-a-z\\d%_.~+]*)*'+ // port and path
  '(\\?[;&a-z\\d%_.~+=-]*)?'+ // query string
  '(\\#[-a-z\\d_]*)?$','i'); // fragment locator
  return !!regex.test(url);
}

export function updateCanvas(url:string){
    
  if (document.type !== 'undefined' && checkCorrecURl(url)){
      const canvas = document.getElementById('myCanvas') as HTMLCanvasElement;
      const ctx = canvas.getContext('2d');

      ctx.clearRect(0, 0, canvas.width, canvas.height);

      // Create an image object
      const img = new Image();
      img.src = url;

      img.onload = function() {
          width = this.width;
          height = this.height;
          

          // Draw the image on the canvas
          ctx.drawImage(img, 0, 0, canvas.width, canvas.height);

          // Define the text style
          ctx.fillStyle = 'black';
          ctx.font = '16px Arial';

          // Get the image's source and measure its width
          const sourceText = 'Source: ' + getSource(url);
          const textWidth = ctx.measureText(sourceText).width;

          // Draw the text on the bottom-left corner
          ctx.fillText(sourceText, canvas.width - 200, canvas.height - 10);
      };
      console.log('canvas updated');
    }
  }

onMount(async () => {
  updateCanvas(url);
});
</script>
<div class="relative w-1/2">
    <label for="UserEmail" class="sr-only"> Email </label>
  
    <input
      type="text"
      id="url"
      placeholder="url"
      class="w-full rounded-md border-gray-200 pe-10 shadow-sm sm:text-sm"
        bind:value={url}
        on:input={() => updateCanvas(url)}
        on:change={() => updateCanvas(url)}
    />
  
    <span
      class="pointer-events-none absolute inset-y-0 end-0 grid w-10 place-content-center text-gray-500"
    >
      <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 20 20" fill="currentColor" class="h-4 w-4">
        <path
          fill-rule="evenodd"
          d="M5.404 14.596A6.5 6.5 0 1116.5 10a1.25 1.25 0 01-2.5 0 4 4 0 10-.571 2.06A2.75 2.75 0 0018 10a8 8 0 10-2.343 5.657.75.75 0 00-1.06-1.06 6.5 6.5 0 01-9.193 0zM10 7.5a2.5 2.5 0 100 5 2.5 2.5 0 000-5z"
          clip-rule="evenodd"
        />
      </svg>
    </span>
</div>

<canvas id="myCanvas" width={width} height={height}></canvas>
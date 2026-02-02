<script lang="ts">
  import Tooltip from "./Tooltip.svelte";
  type Stage = "question" | "captcha" | "success";

  interface MathQuestion {
    question: string;
    answer: string;
  }

  let stage: Stage = "question";
  let captchaLevel: number = 0;
  let userAnswer: string = "";
  let showError: boolean = false;
  let showWarningPopup: boolean = false;

  const generateMathQuestion = (level: number): MathQuestion => {
    // Seed-based randomization for consistency at each level
    const seed: number = level * 12345;
    const random = (min: number, max: number): number => {
      const x: number = Math.sin(seed + min + max) * 10000;
      return Math.floor(min + (x - Math.floor(x)) * (max - min + 1));
    };

    if (level === 0) {
      // Simple addition
      const a: number = random(2, 15);
      const b: number = random(2, 15);
      return { question: `whats ${a} + ${b}?`, answer: String(a + b) };
    } else if (level === 1) {
      // Addition and subtraction
      const a: number = random(15, 40);
      const b: number = random(5, 20);
      return { question: `whats ${a} - ${b}?`, answer: String(a - b) };
    } else if (level === 2) {
      // Multi-step arithmetic
      const a: number = random(5, 15);
      const b: number = random(3, 10);
      const c: number = random(2, 8);
      return {
        question: `whats ${a} + ${b} - ${c}?`,
        answer: String(a + b - c),
      };
    } else if (level === 3) {
      // Simple multiplication
      const a: number = random(6, 15);
      const b: number = random(4, 12);
      return { question: `whats ${a} × ${b}?`, answer: String(a * b) };
    } else if (level === 4) {
      // Larger multiplication
      const a: number = random(15, 35);
      const b: number = random(12, 25);
      return { question: `whats ${a} × ${b}?`, answer: String(a * b) };
    } else if (level === 5) {
      // Power rule integral
      const n: number = random(2, 5);
      const coef: number = random(2, 8);
      const newN: number = n + 1;
      return {
        question: `whats ∫${coef}x^${n} dx? (Answer format: x^a where a is the exponent, ignore constant)`,
        answer: `x^${newN}`,
      };
    } else if (level === 6) {
      // Basic polynomial integral
      const a: number = random(2, 6);
      const b: number = random(3, 9);
      return {
        question: `whats ∫(${a}x + ${b}) dx? (Answer format: ax^2+bx)`,
        answer: `${a}/2x^2+${b}x`,
      };
    } else if (level === 7) {
      // Definite integral with bounds
      const a: number = random(1, 4);
      const upper: number = random(2, 5);
      const result: number = (a * upper * upper) / 2;
      return {
        question: `whats ∫ from 0→${upper} of ${a}x dx? (Round to nearest integer if needed)`,
        answer: String(Math.round(result)),
      };
    } else if (level === 8) {
      // Exponential integral
      const a: number = random(2, 7);
      return {
        question: `whats ∫${a}e^x dx? (Answer format: ae^x, ignore constant)`,
        answer: `${a}e^x`,
      };
    } else if (level === 9) {
      // Trigonometric integral - sin
      const a: number = random(2, 8);
      return {
        question: `whats ∫${a}cos(x) dx? (Answer format: asin(x), ignore constant)`,
        answer: `${a}sin(x)`,
      };
    } else if (level === 10) {
      // Trigonometric integral - cos
      const a: number = random(3, 9);
      return {
        question: `whats ∫${a}sin(x) dx? (Answer format: -acos(x), ignore constant)`,
        answer: `-${a}cos(x)`,
      };
    } else if (level === 11) {
      // Polynomial with multiple terms
      const a: number = random(2, 5);
      const b: number = random(3, 7);
      const c: number = random(2, 6);
      return {
        question: `whats ∫(${a}x^2 + ${b}x + ${c}) dx? (Format: ax^3+bx^2+cx, ignore constant)`,
        answer: `${a}x^3+${b}x^2+${c}x`,
      };
    } else if (level === 12) {
      // Chain rule backwards
      const a: number = random(2, 6);
      const b: number = random(2, 5);
      const n: number = random(2, 4);
      return {
        question: `whats ∫${a}(${b}x)^${n} dx? (Answer format: a(bx)^c where c=${n + 1}, ignore constant)`,
        answer: `${a}(${b}x)^${n + 1}`,
      };
    } else if (level === 13) {
      // Reciprocal integral
      const a: number = random(2, 8);
      return {
        question: `whats ∫${a}/x dx? (Answer format: aln(x), ignore constant)`,
        answer: `${a}ln(x)`,
      };
    } else if (level === 14) {
      // Complex polynomial
      const a: number = random(1, 4);
      const b: number = random(2, 5);
      const c: number = random(3, 7);
      const d: number = random(2, 6);
      return {
        question: `whats ∫(${a}x^3 + ${b}x^2 + ${c}x + ${d}) dx? (Format: ax^4+bx^3+cx^2+dx, ignore constant)`,
        answer: `${a}x^4+${b}x^3+${c}x^2+${d}x`,
      };
    } else {
      // Increasingly complex integrals
      const complexity: number = level - 14;
      const a: number = random(2, 5 + complexity);
      const b: number = random(3, 7 + complexity);
      const n: number = random(3, 5 + complexity);
      const m: number = random(2, 4 + complexity);
      return {
        question: `whats ∫(${a}x^${n} + ${b}x^${m}) dx? (Format: ax^b+cx^d, ignore constant)`,
        answer: `${a}x^${n + 1}+${b}x^${m + 1}`,
      };
    }
  };

  $: currentCaptcha = generateMathQuestion(captchaLevel);
  $: progress = Math.min((captchaLevel / 20) * 100, 100);

  function handleYes(): void {
    stage = "success";
  }

  function handleNo(): void {
    showWarningPopup = true;
  }

  function proceedToCaptcha(): void {
    showWarningPopup = false;
    stage = "captcha";
    captchaLevel = 0;
    userAnswer = "";
    showError = false;
  }

  function handleCaptchaSubmit(): void {
    if (userAnswer.trim() === currentCaptcha.answer) {
      captchaLevel += 1;
      userAnswer = "";
      showError = false;
    } else {
      showError = true;
    }
  }

  function goBackToHome(): void {
    stage = "question";
    captchaLevel = 0;
    userAnswer = "";
    showError = false;
    showWarningPopup = false;
  }

  function handleInput(e: Event): void {
    const target = e.target as HTMLInputElement;
    userAnswer = target.value;
    showError = false;
  }

  function handleKeyDown(e: KeyboardEvent): void {
    if (e.key === "Enter") {
      handleCaptchaSubmit();
    }
  }
</script>

{#if stage === "success"}
  <div
    class="min-h-screen min-w-screen flex items-center justify-center relative"
  >
    <button
      on:click={goBackToHome}
      class="absolute top-6 left-6 flex items-center gap-2 text-black hover:text-gray-200 transition-colors"
    >
      <svg
        class="w-5 h-5"
        xmlns="http://www.w3.org/2000/svg"
        viewBox="0 0 24 24"
        fill="none"
        stroke="currentColor"
        stroke-width="2"
      >
        <path d="M19 12H5M12 19l-7-7 7-7" />
      </svg>
      <span class="font-semibold">back</span>
    </button>

    <div
      class="bg-white rounded-3xl border-black border p-12 max-w-lg w-full text-center"
    >
      <div class="mb-6 flex justify-center">
        <svg
          class="w-24 h-24 text-red-500 fill-red-500 animate-ping"
          xmlns="http://www.w3.org/2000/svg"
          viewBox="0 0 24 24"
          fill="currentColor"
        >
          <path
            d="M11.645 20.91l-.007-.003-.022-.012a15.247 15.247 0 01-.383-.218 25.18 25.18 0 01-4.244-3.17C4.688 15.36 2.25 12.174 2.25 8.25 2.25 5.322 4.714 3 7.688 3A5.5 5.5 0 0112 5.052 5.5 5.5 0 0116.313 3c2.973 0 5.437 2.322 5.437 5.25 0 3.925-2.438 7.111-4.739 9.256a25.175 25.175 0 01-4.244 3.17 15.247 15.247 0 01-.383.219l-.022.012-.007.004-.003.001a.752.752 0 01-.704 0l-.003-.001z"
          />
        </svg>
      </div>
      <h1 class="text-4xl font-bold text-gray-800 mb-4">YAYAYAYAYAYAYA</h1>
      <p class="text-xl text-gray-600 mb-6">i love you so much baby!!</p>
      <button
        class="text-2xl mb-4 text-red-600 hover:text-black transition-colors"
        on:click={() =>
          (window.location.href = "https://valentines.nesetk.com/")}
      >
        now for the next step.. click on me
      </button>
    </div>
  </div>
{:else if stage === "captcha"}
  <div
    class="min-h-screen min-w-screen flex items-center justify-center p-4 relative"
  >
    <button
      on:click={goBackToHome}
      class="absolute top-6 left-6 flex items-center gap-2 text-black hover:text-gray-300 transition-colors"
    >
      <svg
        class="w-5 h-5"
        xmlns="http://www.w3.org/2000/svg"
        viewBox="0 0 24 24"
        fill="none"
        stroke="currentColor"
        stroke-width="2"
      >
        <path d="M19 12H5M12 19l-7-7 7-7" />
      </svg>
      <span class="font-semibold">back</span>
    </button>

    <div class="bg-white rounded-2xl border border-black p-8 max-w-md w-full">
      <div class="mb-6">
        <div class="flex items-center mb-2">
          <h2 class="text-2xl font-bold text-gray-800">
            human verification required in order to say no
          </h2>
        </div>
        <div class="w-full bg-gray-200 rounded-full h-2">
          <div
            class="bg-red-500 h-2 rounded-full transition-all duration-500"
            style="width: {progress}%"
          ></div>
        </div>
        <p class="text-sm text-gray-500 mt-2 w-fit pr-5">
          <Tooltip title="how much love i have for you">
            captcha {captchaLevel + 1} of infinity
          </Tooltip>
        </p>
      </div>

      <div>
        <div class="mb-6">
          <label class="block text-gray-700 font-semibold mb-3 text-left">
            {currentCaptcha.question}
          </label>
          <input
            type="text"
            bind:value={userAnswer}
            on:input={handleInput}
            on:keydown={handleKeyDown}
            class="w-full px-4 py-3 border-2 border-gray-300 rounded-lg focus:border-red-500 focus:outline-none"
            placeholder="type ur answer here yo"
            autofocus
          />
          {#if showError}
            <p class="text-red-500 text-sm mt-2 text-left">
              SOMEONE needs to go and study.. try again
            </p>
          {/if}
        </div>

        <button
          on:click={handleCaptchaSubmit}
          class="w-full bg-gray-800 hover:bg-gray-900 text-white font-semibold py-3 px-6 rounded-lg transition-colors"
        >
          submit answer
        </button>
      </div>

      <div class="mt-6 text-center">
        <p class="text-sm text-gray-500 mb-2">ts seems tedious...</p>
        <button
          on:click={goBackToHome}
          class="text-red-500 hover:text-red-600 font-semibold text-sm bg-transparent border-none cursor-pointer"
        >
          wouldnt it be easier to reconsider?
        </button>
      </div>
    </div>
  </div>
{:else}
  <div class="min-h-screen min-w-screen flex items-center justify-center">
    <div
      class="rounded-3xl p-12 max-w-md border-black border justify-center w-full text-center"
    >
      <div class="mb-8 flex justify-center text-2xl animate-bounce">
        hi baby
      </div>

      <h1 class="text-5xl font-bold text-gray-800 mb-8">
        will u be my valentine? :3
      </h1>

      <div class="space-y-4">
        <button
          on:click={handleYes}
          class="w-full bg-red-500 hover:bg-red-600 text-white font-bold py-4 px-8 rounded-xl text-xl transition-all transform hover:scale-105 shadow-lg"
        >
          yes ilysm
        </button>

        <Tooltip title="PLS DONT CLICK THIS">
          <button
            on:click={handleNo}
            class="w-full bg-gray-300 hover:bg-gray-400 text-gray-700 font-bold py-4 px-8 rounded-xl text-xl transition-all"
          >
            no (i hate u)
          </button>
        </Tooltip>
      </div>
    </div>
  </div>
{/if}

{#if showWarningPopup}
  <div class="fixed inset-0 flex items-center justify-center p-4 z-50">
    <div class="absolute inset-0 bg-black opacity-20"></div>

    <div
      class="bg-white rounded-2xl p-8 max-w-sm w-full text-center relative z-10"
    >
      <h1 class="font-bold text-gray-800 mb-4">uh oh!</h1>
      <p class="text-lg text-gray-800 mb-6">
        in order to say no, you need to prove that you're a human...
      </p>
      <button
        on:click={proceedToCaptcha}
        class="w-full bg-blue-300 hover:bg-blue-400 text-black py-3 px-6 rounded-lg transition-colors"
      >
        proceed →
      </button>
    </div>
  </div>
{/if}

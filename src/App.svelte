<script lang="ts">
  import { notification } from "@kksh/api/ui/custom";
  import { ThemeWrapper, Input } from "@kksh/svelte5";
  import { onMount } from "svelte";

  let question = "";
  let answer = "";
  let loading = false;
  let showConfig = false;
  let apiKey = "";

  onMount(() => {
    apiKey = localStorage.getItem("gemini-api-key") || "";
  });

  async function askQuestion() {
    if (!apiKey) {
      notification.sendNotification("Please configure your API key first");
      showConfig = true;
      return;
    }

    loading = true;
    try {
      const response = await fetch(
        `https://generativelanguage.googleapis.com/v1beta/models/gemini-2.0-flash-exp:generateContent?key=${apiKey}`,
        {
          method: "POST",
          headers: {
            "Content-Type": "application/json",
          },
          body: JSON.stringify({
            systemInstruction: {
              role: "user",
              parts: [
                {
                  text: "Answer inquired questions in a short and concise manner.",
                },
              ],
            },
            contents: [
              {
                parts: [
                  {
                    text: question,
                  },
                ],
              },
            ],
          }),
          mode: "cors",
        }
      );

      if (!response.ok) {
        const errorData = await response.json();
        console.error("API Error:", errorData);
        notification.sendNotification(
          `Error: ${response.status} - ${errorData.error.message}`
        );
        return;
      }

      const data = await response.json();
      answer = data.candidates[0].content.parts[0].text;
    } catch (error) {
      console.error("Fetch Error:", error);
      notification.sendNotification(`Fetch Error: ${error}`);
    } finally {
      loading = false;
    }
  }

  function saveConfig() {
    localStorage.setItem("gemini-api-key", apiKey);
    notification.sendNotification("API key saved successfully");
    showConfig = false;
  }

  function handleKeyDown(event: KeyboardEvent) {
    if (event.key === "Enter" && event.ctrlKey) {
      event.preventDefault();
      askQuestion();
    }
  }
</script>

<ThemeWrapper>
  <div class="h-screen p-4 space-y-4">
    {#if showConfig}
      <div class="p-4 space-y-4">
        <h2 class="text-xl font-bold">Configuration</h2>
        <div class="space-y-2">
          <label for="apiKey">Gemini API Key</label>
          <Input
            type="password"
            id="apiKey"
            bind:value={apiKey}
            placeholder="Enter your Gemini API key"
          />
        </div>
        <button
          class="px-4 py-2 bg-gray-200 hover:bg-gray-300 rounded-md"
          on:click={saveConfig}>Save Configuration</button
        >
      </div>
    {:else}
      <div class="space-y-4">
        <div class="flex justify-between">
          <h1 class="text-2xl font-bold">Ask AI</h1>
          <button
            class="px-4 py-2 bg-gray-200 hover:bg-gray-300 rounded-md"
            on:click={() => (showConfig = true)}
          >
            Configure API Key
          </button>
        </div>

        <div class="space-y-2">
          <textarea
            class="w-full p-2 border rounded-md"
            bind:value={question}
            placeholder="Enter your question here..."
            rows="4"
            on:keydown={handleKeyDown}
          ></textarea>
          <button
            class="px-4 py-2 bg-blue-500 text-white rounded-md"
            on:click={askQuestion}
            disabled={loading}
          >
            {loading ? "Asking..." : "Ask AI"}
          </button>
        </div>

        {#if answer}
          <div class="p-4 border rounded-md bg-muted">
            <h3 class="font-semibold mb-2">Answer:</h3>
            <p class="whitespace-pre-wrap">{answer}</p>
          </div>
        {/if}
      </div>
    {/if}
  </div>
</ThemeWrapper>

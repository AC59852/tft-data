<script>
  let totalCount = 0;

  onload = () => {
    // Check if there's data stored in localStorage
    const storedData = localStorage.getItem('riotApiData');
    console.log("Stored data:", storedData)
    if (storedData) {
        const { lastPage, lastTotalCount } = JSON.parse(storedData);
        totalCount = lastTotalCount;
    }
  };

  const getUsersCount = async () => {
    const apiKey = 'RGAPI-aa69cf77-b9b2-4321-9235-add596459957';
    const BASE_URL = 'https://na1.api.riotgames.com/tft/league/v1';
    const pageSize = 100; // Assuming each page has 100 items
    let page = 1;

    // Check if there's data stored in localStorage
    const storedData = localStorage.getItem('riotApiData');
    console.log("Stored data:", storedData)
    if (storedData) {
        const { lastPage, lastTotalCount } = JSON.parse(storedData);
        page = lastPage;
        totalCount = lastTotalCount;
    }

    while (true) {
        try {
            const response = await fetch(`${BASE_URL}/entries/SILVER/IV?queue=RANKED_TFT_DOUBLE_UP&page=${page}&api_key=${apiKey}`);

            if (!response.ok) {
                // Check if the error is a rate limit error
                if (response.status === 429) {
                    console.log("Rate limit exceeded. Saving current progress.");
                    localStorage.setItem('riotApiData', JSON.stringify({ lastPage: page, lastTotalCount: totalCount }));
                    return totalCount; // Return the count retrieved so far
                } else {
                    throw new Error(`Error: ${response.status} - ${response.statusText}`);
                }
            }

            const usersData = await response.json();

            // If usersData is empty, we have reached the last page
            if (usersData.length === 0) {
                localStorage.setItem('riotApiData', JSON.stringify({ lastPage: page, lastTotalCount: totalCount }));
                break;
            }

            totalCount += usersData.length;
            page++;

        } catch (error) {
            console.error("Request error:", error);
            // If the error is due to too many requests, save the current progress
            if (error instanceof TypeError && error.message === "Failed to fetch") {
                console.log("Rate limit exceeded. Saving current progress.");
                localStorage.setItem('riotApiData', JSON.stringify({ lastPage: page - 1, lastTotalCount: totalCount }));
                return totalCount;
            }
            return totalCount; // Return the count retrieved so far even on error
        }
    }

    return totalCount;
  };

  const fetchData = async () => {
    totalCount = await getUsersCount();
  };
</script>
<button on:click={fetchData}>Fetch Data</button>
{#if totalCount === 0}
  <p>No data fetched yet.</p>
{:else}
  <h2>Total Count: {totalCount}</h2>
{/if}
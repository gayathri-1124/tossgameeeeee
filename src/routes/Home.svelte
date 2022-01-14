<script>
  import axios from "axios";
  export let params = {};
  let username = "";
  let roomLocked = false;
  let isAdmin = true;
  let isUsernameEmpty = true;
  let listOfUsers = [];
  let baseURL = "http://localhost:3000/";

  let imgs = [
    "https://media.giphy.com/media/6jqfXikz9yzhS/giphy.gif",
    "https://media.giphy.com/media/xT41JICjl8yyc/giphy.gif",
    "https://media.giphy.com/media/hWk4OcIkPDfSCon46I/giphy.gif",
    "https://media.giphy.com/media/l4FGGCajbJh4hV3qw/giphy.gif",
    "https://media.giphy.com/media/15lAWKdLcMm8o/giphy-downsized-large.gif",
  ]; //xT41JICjl8yyc
  let statusText = [
    "we are waiting",
    "room is locked, tossing soon",
    "tossing in progress",
    "heads , its heads",
    "tails it is",
  ];
  let status = 4;

  function startlongpolling() {
    //set long pooling
    let a = setInterval(() => {
      console.log("bro");
      axios
        .post(baseURL + "getdata", {
          username: username,
          sessionID: params.roomID,
        })
        .then((res) => {
          listOfUsers = res.data.participants;
          status = res.data.status;
          isAdmin = res.data.admin === username;
          roomLocked = res.data.roomLocked;
          isUsernameEmpty = false;
          if (res.data.result != -1) {
            status = res.data.result;
          }
        });
    }, 2000);
  }
</script>

<video autoplay muted loop class=" vid">
  <source src="asset/star.mp4" class="" type="video/mp4" />
</video>

{#if isUsernameEmpty === true}
  <section class="h-screen w-screen  flex justify-center items-center">
    <form class="w-full mx-96">
      <div class="text-white my-3 text-2xl">logging into {params.roomID}</div>
      <input
        type="text"
        bind:value={username}
        placeholder="Wassup? whats your name"
        class="p-5 w-full text-2xl bg-gray-800 text-white rounded-lg outline-none"
      />
      <div
        on:click={() => {
          //server call
          axios
            .post(baseURL + "registersession", {
              username: username,
              sessionID: params.roomID,
            })
            .then((res) => {
              if (res.data.message === "locked") {
                alert("room locked");
                return;
              }
              listOfUsers = res.data.participants;
              status = res.data.status;
              isAdmin = res.data.admin === username;
              roomLocked = res.data.roomLocked;
              isUsernameEmpty = false;
              startlongpolling();
            });
        }}
        class="p-5 text-2xl flex items-center gap-5 bg-indigo-500 rounded-2xl w-fit mt-5 hover:bg-indigo-400 transition-all cursor-pointer  text-white "
      >
        Take me into the roooooom
        <svg
          xmlns="http://www.w3.org/2000/svg"
          class="h-6 w-6"
          fill="none"
          viewBox="0 0 24 24"
          stroke="currentColor"
        >
          <path
            stroke-linecap="round"
            stroke-linejoin="round"
            stroke-width="2"
            d="M13 7l5 5m0 0l-5 5m5-5H6"
          />
        </svg>
      </div>
    </form>
  </section>
{:else}
  <section class="h-screen w-screen  grid grid-cols-3 text-white">
    <div class=" bg-gray-800 m-10 rounded-3xl p-5">
      <div class="m-5 text-3xl font-bold">Space toss</div>
      {#if isAdmin}
        <div class="m-5 flex gap-2">
          {#if roomLocked}
            <div
              on:click={() => {
                axios
                  .post(baseURL + "tosscoin", { sessionID: params.roomID })
                  .then((res) => {});
              }}
              class="p-3 rounded-2xl hover:bg-gray-700 cursor-pointer"
            >
              toss coin
            </div>
          {:else}
            <div
              on:click={() => {
                axios
                  .post(baseURL + "lockroom", { sessionID: params.roomID })
                  .then((res) => {
                    if (res.data.message === "locked") {
                      roomLocked = true;
                    }
                  });
              }}
              class="p-3 rounded-2xl hover:bg-gray-700 cursor-pointer flex items-center gap-2"
            >
              <svg
                xmlns="http://www.w3.org/2000/svg"
                class="h-6 w-6"
                fill="none"
                viewBox="0 0 24 24"
                stroke="currentColor"
              >
                <path
                  stroke-linecap="round"
                  stroke-linejoin="round"
                  stroke-width="2"
                  d="M12 15v2m-6 4h12a2 2 0 002-2v-6a2 2 0 00-2-2H6a2 2 0 00-2 2v6a2 2 0 002 2zm10-10V7a4 4 0 00-8 0v4h8z"
                />
              </svg>
              lock room
            </div>
          {/if}
        </div>
      {/if}

      <div class="m-5 ">in da room, we have</div>
      <div class="flex my-10 ml-5 flex-col gap-5">
        {#each listOfUsers as user}
          <div
            class="flex gap-5 items-center  hover:bg-gray-900 p-2 rounded-xl"
          >
            <img
              src="https://joeschmoe.io/api/v1/random"
              class="bg-white w-10 h-10 rounded-full
              "
              alt=""
            />
            <div>{user}</div>
          </div>
        {/each}
      </div>
    </div>
    <div
      class="bg-gray-700 m-10 rounded-3xl col-span-2 flex relative justify-center items-center"
    >
      <img
        class=" w-full h-full  rounded-3xl object-cover"
        src={imgs[status]}
      />
      <div class="absolute text-3xl font-bold">{statusText[status]}</div>
    </div>
  </section>
{/if}

<style>
  .w-fit {
    width: fit-content;
  }
  .vid {
    position: fixed;
    right: 0;
    bottom: 0;
    min-width: 100%;
    min-height: 100%;
    z-index: -1;
  }
</style>

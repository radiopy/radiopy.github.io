# Radiopy

Radiopy is a project which lets you listen music from radio stations within Spotify.
<br>
<details>
    <summary>How does it work?</summary>
    Many radio stations have a playlist feature where you can see what songs have been played at what time.
    My script uses this feature to find out what has been playing all week. It then looks up those songs on Spotify and returns an updated playlist.
    These playlists are updated once a week.
</details>

<details>
    <summary>But why the effort?</summary>
    I started this project because I wanted to know what was playing on the radio without having to tune in.
    I somewhat dislike moderators, commercials, already shortened songs being shortened *again* by the station, unnecessary sound effects between songs, ...
    Besides, I already pay for Spotify, so why not?
    <br>
    Also it's fun.
</details>

Here's a collection of all radio stations that the project currently supports:

<div class="channels"></div>
<script>
    const channels = document.getElementsByClassName("channels")[0]

    async function getData() {
        const url = "https://radiopy.dieserniko.link/metadata.json";
        const response = await fetch(url);
        if (!response.ok) {
            throw new Error(`Response status: ${response.status}`);
        }

        return await response.json();
    }

    function createObjects(data) {
        const objects = [];
        data.forEach(function (item, index) {
            const newObject = document.createElement("details");
            const newSummary = document.createElement("summary");
            newObject.append(newSummary);
            if ("image" in item) {
                const newImage = document.createElement("img");
                newImage.src = item["image"];
                newImage.width = 20;
                newImage.loading = "lazy";
                newSummary.appendChild(newImage);
            }
            const newContent = document.createTextNode(" " + item["name"]);
            newSummary.appendChild(newContent);
            if ("url" in item) {
                const newLink = document.createElement("a");
                newLink.href = item["url"];
                newLink.target = "_blank";
                newLink.rel = "noopener noreferrer";
                newLink.textContent = "🔗";
                newObject.appendChild(newLink);
            }
            if ("description" in item) {
                const newDescription = document.createElement("p");
                newDescription.textContent = item["description"];
                newObject.appendChild(newDescription);
            }
            if ("playlist" in item) {
                const newPlaylist = document.createElement("iframe");
                // create Spotify embed (playlist value is the Spotify URI)
                newPlaylist.src = "https://open.spotify.com/embed/playlist/" + item["playlist"];
                newPlaylist.width = 300;
                newPlaylist.height = 380;
                newPlaylist.frameborder = 0;
                newPlaylist.loading = "lazy";
                newObject.appendChild(newPlaylist);
            }
            if ("children" in item) {
                createObjects(item["children"]).forEach(function (child, index) {
                    const newUl = document.createElement("ul");
                    newUl.appendChild(child);
                    newObject.appendChild(newUl);
                });
            }
            objects.push(newObject);
        })
        return objects;
    }

    async function main() {
        const data = await getData();
        console.log(data);
        const objects = createObjects(data);
        console.log(objects);
        objects.forEach(function (item, index) {
            channels.appendChild(item);
        });

    }

    console.log("hi");
    main();
</script>

# Radiopy

Hi there. This is not finished yet, but the result might somewhat look like this.

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
            const newUl = document.createElement("ul");
            const newObject = document.createElement("details");
            newUl.append(newObject);
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
                    newObject.appendChild(child);
                });
            }
            objects.push(newUl);
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

</ul>

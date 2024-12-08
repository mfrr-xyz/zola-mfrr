+++
title = "Posts"
+++

## Bluesky
<div id="feed"></div>

<script>
    const apiUrl = 'https://public.api.bsky.app/xrpc/app.bsky.feed.getAuthorFeed?actor=did%3Aplc%3A3nvaxxnly2lxecfcydtxww6l&filter=posts_and_author_threads&includePins=true&limit=30';

    //suppress JSUnusedLocalSymbols
    function createPosts(data) {
        const feedContainer = document.getElementById('feed');
        feedContainer.innerHTML = '';

        data.feed.forEach(item => {
            const post = item.post;
            const postElement = document.createElement('div');
            postElement.className = 'post';
            
            const authorInfo = `
                <div class="post-meta">
                    <img src="${post.author.avatar}" alt="${post.author.displayName}" width="30" height="30" style="border-radius: 50%; vertical-align: middle;">
                    <strong>${post.author.displayName}</strong> @${post.author.handle}
                    <br>
                    ${new Date(post.record.createdAt).toLocaleString()}
                </div>
            `;
            
            const postContent = `
                <p>${post.record.text}</p>
                ${post.embed && post.embed.images ? `<img src="${post.embed.images[0].fullsize}" alt="${post.embed.images[0].alt}">` : ''}
                <div class="post-meta">
                    Replies: ${post.replyCount} | Reposts: ${post.repostCount} | Likes: ${post.likeCount}
                </div>
            `;
            
            postElement.innerHTML = authorInfo + postContent;
            feedContainer.appendChild(postElement);
        });
    }

    function showLoading() {
        const feedContainer = document.getElementById('feed');
        feedContainer.innerHTML = '<div class="loading">Loading...</div>';
    }

    function showError(message) {
        const feedContainer = document.getElementById('feed');
        feedContainer.innerHTML = `<div class="error">Error: ${message}</div>`;
    }

    async function fetchPosts() {
        showLoading();
        try {
            const response = await fetch(apiUrl);
            if (!response.ok) {
                throw new Error(`HTTP error! status: ${response.status}`);
            }
            const data = await response.json();
            createPosts(data);
        } catch (error) {
            console.error('Fetching error:', error);
            showError('Failed to load posts. Please try again later.');
        }
    }

    // Call the function to fetch and create posts
    fetchPosts();
</script>
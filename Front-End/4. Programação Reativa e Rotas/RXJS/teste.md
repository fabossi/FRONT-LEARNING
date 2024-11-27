```markdown
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Fixed Navigation Menu</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            margin: 0;
            padding: 0;
        }
        .fixed-nav {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            background-color: #333;
            color: white;
            padding: 10px 0;
            z-index: 1000;
        }
        .fixed-nav ul {
            list-style-type: none;
            margin: 0;
            padding: 0;
            display: flex;
            justify-content: center;
        }
        .fixed-nav ul li {
            margin: 0 15px;
        }
        .fixed-nav ul li a {
            color: white;
            text-decoration: none;
        }
        .content {
            margin-top: 60px; /* Adjust based on nav height */
            padding: 20px;
        }
    </style>
</head>
<body>
    <nav class="fixed-nav">
        <ul>
            <li><a href="#section1">Section 1</a></li>
            <li><a href="#section2">Section 2</a></li>
            <li><a href="#section3">Section 3</a></li>
        </ul>
    </nav>

    <div class="content">
        <h1 id="section1">Section 1</h1>
        <p>Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nullam in dui mauris. Vivamus hendrerit arcu sed erat molestie vehicula.</p>
        
        <h1 id="section2">Section 2</h1>
        <p>Sed auctor neque eu tellus rhoncus ut eleifend nibh porttitor. Ut in nulla enim. Phasellus molestie magna non est bibendum non venenatis nisl tempor.</p>
        
        <h1 id="section3">Section 3</h1>
        <p>Suspendisse dictum feugiat nisl ut dapibus. Mauris iaculis porttitor posuere. Praesent id metus massa, ut blandit odio.</p>
        
        <!-- Add more content to demonstrate scrolling -->
        <div style="height: 2000px;">
            More content to scroll through...
        </div>
    </div>
</body>
</html>
```

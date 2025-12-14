# funny
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Notification & Pop-up Creator</title>
    <!-- Load Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        /* Custom styles for PWA look and feel */
        body {
            font-family: 'Inter', sans-serif;
            background-color: #f7f9fc;
        }
        /* Mobile-friendly large touch targets */
        .btn-large {
            padding: 1rem 1.5rem;
            font-size: 1.125rem;
            font-weight: 600;
            transition: all 0.15s ease-in-out;
            box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.1), 0 2px 4px -2px rgba(0, 0, 0, 0.1);
        }
        .btn-large:hover {
            transform: translateY(-1px);
            box-shadow: 0 10px 15px -3px rgba(0, 0, 0, 0.1), 0 4px 6px -4px rgba(0, 0, 0, 0.1);
        }
        .input-field {
            padding: 0.75rem 1rem;
            border: 2px solid #e2e8f0;
            background-color: white;
            transition: border-color 0.2s;
        }
        .input-field:focus {
            border-color: #4f46e5;
            outline: none;
            box-shadow: 0 0 0 3px rgba(79, 70, 229, 0.2);
        }

        /* --- Custom Modal Styles for iOS look --- */

        /* Base style for both light and dark modes */
        .ios-alert-base {
            max-width: 300px; /* Small screen size */
            padding: 1rem; /* Less padding than the main app card */
            border-radius: 16px; /* Very rounded corners */
            text-align: center;
            box-shadow: 0 10px 25px rgba(0, 0, 0, 0.2); /* Add shadow for realism */
            /* Transition for the pop-in/pop-out effect */
            transition: transform 0.2s ease-out, opacity 0.2s ease-out, background-color 0.2s, color 0.2s;
        }
        
        /* Hide state for the modal content */
        .ios-alert-base.hidden-state {
            opacity: 0;
            transform: scale(0.9);
        }

        /* Light/White Style (Standard iOS Alert) */
        .ios-alert-light {
            background-color: white;
        }
        .ios-alert-light .ios-title { color: #1f2937; /* Gray-900 */ }
        .ios-alert-light .ios-message { color: #4b5563; /* Gray-600 */ }
        .ios-alert-light .ios-button-wrapper { border-top: 1px solid #e0e0e0; }
        .ios-alert-light .ios-button:active {
            background-color: rgba(224, 224, 224, 0.5); 
        }

        /* Dark/Black Style (Modern Overlay/Toast) */
        .ios-alert-dark {
            background-color: #2c2c2e; /* Dark Gray / Black */
        }
        .ios-alert-dark .ios-title { color: white; }
        .ios-alert-dark .ios-message { color: #a1a1aa; /* Zinc-400 */ }
        .ios-alert-dark .ios-button-wrapper { border-top: 1px solid #48484a; /* Darker Separator */ }
        .ios-alert-dark .ios-button {
            color: #4dc2f8; /* Lighter blue for dark background */
        }
        .ios-alert-dark .ios-button:active {
            background-color: #48484a; 
        }

        /* iOS-style button wrapper for bottom action */
        .ios-button-wrapper {
            margin-top: 1rem;
            padding-top: 0;
        }
        /* iOS-style button */
        .ios-button {
            display: block;
            width: 100%;
            padding: 0.75rem 0;
            color: #007aff; /* iOS blue color (default/light) */
            font-weight: 600;
            background: transparent;
            border: none;
            transition: background-color 0.1s;
        }

    </style>
    <!-- iOS PWA Meta Tags (Crucial for "Add to Home Screen" experience) -->
    <meta name="apple-mobile-web-app-capable" content="yes">
    <meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
    <meta name="apple-mobile-web-app-title" content="Notifier App">
    <!-- Note: A real PWA would require a separate manifest.json and icon files. -->
</head>
<body class="p-4 sm:p-8 flex items-center justify-center min-h-screen">

    <div class="w-full max-w-lg bg-white p-6 sm:p-8 rounded-xl shadow-2xl space-y-6">
        <h1 class="text-3xl font-extrabold text-indigo-700 text-center">
            Custom Notifier
        </h1>
        <p class="text-center text-gray-500 mb-6">
            Create native system notifications or custom in-app pop-ups.
            <span class="font-semibold text-red-600 block mt-2">
                (On iPad, use Safari 'Share' > 'Add to Home Screen' for best results.)
            </span>
        </p>

        <!-- Notification Status and Controls -->
        <div class="bg-indigo-50 p-4 rounded-lg">
            <h2 class="text-xl font-semibold text-indigo-600 mb-3">System Notification Tools</h2>
            <div id="status-display" class="p-2 text-sm font-medium rounded-md mb-4 text-center">
                Checking permission...
            </div>
            
            <button id="request-permission-btn"
                class="btn-large w-full bg-indigo-600 text-white rounded-lg hover:bg-indigo-700 disabled:opacity-50"
                onclick="requestNotificationPermission()">
                Request Notification Permission
            </button>
        </div>
        
        <!-- Input Fields for Messages -->
        <div class="space-y-4">
            <div>
                <label for="title-input" class="block text-sm font-medium text-gray-700">Notification Title</label>
                <input type="text" id="title-input" value="New Custom Alert" placeholder="Enter title..."
                    class="input-field w-full rounded-lg">
            </div>
            <div>
                <label for="message-input" class="block text-sm font-medium text-gray-700">Notification Message</label>
                <textarea id="message-input" placeholder="Enter message..." rows="2"
                    class="input-field w-full rounded-lg">This is your custom message!</textarea>
            </div>
            <!-- NEW ICON URL INPUT FIELD -->
            <div>
                <label for="icon-url-input" class="block text-sm font-medium text-gray-700">Notification Icon URL (Must be HTTPS)</label>
                <input type="url" id="icon-url-input" 
                    value="https://placehold.co/128x128/10b981/ffffff?text=Alert" 
                    placeholder="Paste a public image URL here (e.g., HTTPS link)"
                    class="input-field w-full rounded-lg">
            </div>
            <!-- END NEW ICON URL INPUT FIELD -->

            <!-- Native Notification Button -->
            <button id="show-native-btn"
                class="btn-large w-full bg-green-600 text-white rounded-lg hover:bg-green-700 disabled:opacity-50"
                onclick="showNativeNotification()">
                Show Instant Native Notification
            </button>
        </div>
        
        <!-- Custom Pop-up Section -->
        <div class="pt-4 border-t border-gray-200 space-y-4">
            <h2 class="text-xl font-semibold text-gray-700">Custom In-App Pop-up</h2>
            
            <!-- POPUP STYLE SELECTION -->
            <div class="flex flex-col sm:flex-row sm:items-center space-y-2 sm:space-y-0 sm:space-x-4">
                <span class="text-sm font-medium text-gray-700">Pop-up Style:</span>
                <label class="inline-flex items-center">
                    <input type="radio" name="popup-style" value="light" checked class="form-radio text-indigo-600 h-4 w-4">
                    <span class="ml-2 text-gray-700">Light (White)</span>
                </label>
                <label class="inline-flex items-center">
                    <input type="radio" name="popup-style" value="dark" class="form-radio text-indigo-600 h-4 w-4">
                    <span class="ml-2 text-gray-700">Dark (Black)</span>
                </label>
            </div>
            <!-- END POPUP STYLE SELECTION -->

            <div>
                <label for="delay-input" class="block text-sm font-medium text-gray-700">Delay (Seconds)</label>
                <input type="number" id="delay-input" value="3" min="1" max="60"
                    class="input-field w-full rounded-lg">
            </div>
            
            <!-- Custom Pop-up Button -->
            <button id="show-custom-btn"
                class="btn-large w-full bg-yellow-500 text-white rounded-lg hover:bg-yellow-600"
                onclick="showCustomPopupWithDelay()">
                Show Custom Pop-up (Delayed)
            </button>
        </div>
    </div>

    <!-- Custom Pop-up Modal (Hidden by default) -->
    <div id="custom-modal" class="hidden fixed inset-0 bg-black bg-opacity-30 z-50 flex items-center justify-center p-4"
        aria-modal="true" role="dialog">
        <!-- iOS-style Alert Box -->
        <div id="modal-content" class="ios-alert-base hidden-state" role="document">
            <h3 id="modal-title" class="ios-title text-lg font-semibold mt-2 mb-1">Custom Pop-up</h3>
            <p id="modal-message" class="ios-message text-sm mb-0 px-2"></p>
            
            <div class="ios-button-wrapper">
                <button class="ios-button" onclick="closeCustomPopup()">
                    Close
                </button>
            </div>
        </div>
    </div>

    <script type="module">
        // Basic Firebase/Auth setup is mandatory for the Canvas environment, 
        // even if not strictly used for this app's core notification logic.
        import { initializeApp } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-app.js";
        import { getAuth, signInAnonymously, signInWithCustomToken } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-auth.js";
        import { getFirestore } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-firestore.js";

        // Global variables provided by the Canvas environment
        const appId = typeof __app_id !== 'undefined' ? __app_id : 'default-app-id';
        const firebaseConfig = typeof __firebase_config !== 'undefined' ? JSON.parse(__firebase_config) : null;
        const initialAuthToken = typeof __initial_auth_token !== 'undefined' ? __initial_auth_token : null;

        let db, auth;
        let notificationPermission = 'default';
        const DEFAULT_ICON_URL = "https://placehold.co/128x128/10b981/ffffff?text=Alert";

        // --- Utility Functions ---

        /**
         * Initializes Firebase and authenticates the user.
         */
        async function initializeFirebase() {
            if (firebaseConfig) {
                try {
                    const app = initializeApp(firebaseConfig);
                    db = getFirestore(app);
                    auth = getAuth(app);
                    
                    if (initialAuthToken) {
                        await signInWithCustomToken(auth, initialAuthToken);
                        console.log("Firebase signed in with custom token.");
                    } else {
                        await signInAnonymously(auth);
                        console.log("Firebase signed in anonymously.");
                    }
                } catch (error) {
                    console.error("Firebase Initialization Error:", error);
                }
            } else {
                console.warn("Firebase configuration not found. Proceeding without database services.");
            }
            updateStatusDisplay(); // Update UI after potential Firebase/Auth setup
        }

        /**
         * Updates the UI based on the current Notification permission status.
         */
        function updateStatusDisplay() {
            const statusDisplay = document.getElementById('status-display');
            const nativeBtn = document.getElementById('show-native-btn');
            const permBtn = document.getElementById('request-permission-btn');

            // Use the standard Notification API status
            notificationPermission = "Notification" in window ? Notification.permission : 'denied';

            switch (notificationPermission) {
                case 'granted':
                    statusDisplay.className = 'p-2 text-sm font-medium rounded-md mb-4 text-center bg-green-100 text-green-700';
                    statusDisplay.textContent = 'Status: GRANTED. Native notifications can be shown.';
                    nativeBtn.disabled = false;
                    permBtn.disabled = true;
                    break;
                case 'denied':
                    statusDisplay.className = 'p-2 text-sm font-medium rounded-md mb-4 text-center bg-red-100 text-red-700';
                    statusDisplay.textContent = 'Status: DENIED. Cannot show native notifications.';
                    nativeBtn.disabled = true;
                    permBtn.disabled = true;
                    break;
                case 'default':
                default:
                    statusDisplay.className = 'p-2 text-sm font-medium rounded-md mb-4 text-center bg-yellow-100 text-yellow-700';
                    statusDisplay.textContent = 'Status: DEFAULT. Please request permission.';
                    nativeBtn.disabled = true;
                    permBtn.disabled = false;
                    break;
            }
        }

        // --- Notification API Functions ---

        /**
         * Requests permission from the user to display notifications.
         */
        window.requestNotificationPermission = async function() {
            if (!("Notification" in window)) {
                showCustomPopup("Error", "This browser does not support desktop notification.");
                return;
            }

            try {
                const permission = await Notification.requestPermission();
                updateStatusDisplay();
                
                if (permission === 'granted') {
                    console.log("Notification permission granted.");
                    // Show a test notification immediately
                    new Notification("Permission Granted!", {
                        body: "You can now send system alerts.",
                        icon: DEFAULT_ICON_URL
                    });
                } else if (permission === 'denied') {
                    console.warn("Notification permission denied.");
                }
            } catch (error) {
                console.error("Error requesting notification permission:", error);
            }
        }

        /**
         * Shows an immediate native system notification.
         */
        window.showNativeNotification = function() {
            if (notificationPermission !== 'granted') {
                showCustomPopup("Error", "Notification permission is not granted. Please request it first.");
                return;
            }

            const title = document.getElementById('title-input').value || "Notification";
            const message = document.getElementById('message-input').value || "Alert from your custom app!";
            // Get the user-provided icon URL, falling back to a default if empty
            const iconUrl = document.getElementById('icon-url-input').value || DEFAULT_ICON_URL;


            try {
                const notification = new Notification(title, {
                    body: message,
                    icon: iconUrl, // Use the user's custom URL
                    tag: 'custom-app-notification-' + Date.now(), // Use a unique tag to prevent merging
                    renotify: true,
                    silent: false
                });

                notification.onclick = (e) => {
                    console.log('Notification clicked:', e);
                    // Optional: Bring the window to the foreground on click
                    window.focus();
                };
                
                console.log(`Native Notification shown: ${title} with icon: ${iconUrl}`);
            } catch (e) {
                console.error("Failed to show native notification:", e);
                showCustomPopup("Native Notification Failed", "Could not show the system notification. Check console for details. Ensure the icon URL is valid and HTTPS.");
            }
        }

        // --- Custom Pop-up Modal Functions ---
        
        /**
         * Shows the custom modal instantly with the iOS-style animation.
         * @param {string} title 
         * @param {string} message 
         */
        function showCustomPopup(title, message) {
            const modal = document.getElementById('custom-modal');
            const modalContent = document.getElementById('modal-content');
            
            // Determine the selected style
            const style = document.querySelector('input[name="popup-style"]:checked').value;
            
            document.getElementById('modal-title').textContent = title;
            document.getElementById('modal-message').textContent = message;
            
            // Apply selected style
            modalContent.classList.remove('ios-alert-light', 'ios-alert-dark');
            if (style === 'dark') {
                modalContent.classList.add('ios-alert-dark');
            } else {
                modalContent.classList.add('ios-alert-light');
            }

            // 1. Make the dark overlay visible
            modal.classList.remove('hidden');
            
            // 2. Wait for paint, then start the pop-in transition
            setTimeout(() => {
                modalContent.classList.remove('hidden-state');
            }, 10); // Small delay for rendering
        }

        /**
         * Hides the custom modal with the iOS-style animation.
         */
        window.closeCustomPopup = function() {
            const modal = document.getElementById('custom-modal');
            const modalContent = document.getElementById('modal-content');
            
            // 1. Start the pop-out transition
            modalContent.classList.add('hidden-state');

            // 2. Wait for the transition to finish (200ms) before hiding the overlay
            setTimeout(() => {
                modal.classList.add('hidden');
            }, 200); 
        }

        /**
         * Schedules the custom modal to show after a delay.
         */
        window.showCustomPopupWithDelay = function() {
            const delay = parseInt(document.getElementById('delay-input').value, 10);
            const title = document.getElementById('title-input').value || "Pop-up Alert";
            const message = document.getElementById('message-input').value || "Delayed custom pop-up message!";
            
            if (isNaN(delay) || delay < 1) {
                showCustomPopup("Error", "Please enter a valid delay (1 second minimum).");
                return;
            }

            // Provide immediate feedback that the timer started
            showCustomPopup("Timer Started", `The custom pop-up will appear in ${delay} seconds.`);

            setTimeout(() => {
                // Rerun the show function to display the actual message with the selected style
                showCustomPopup(title, message);
            }, delay * 1000); // Convert seconds to milliseconds
        }


        // --- Initialization ---

        window.onload = function() {
            // 1. Initialize Firebase (mandatory)
            initializeFirebase(); 
            
            // 2. Check and update notification permission status
            if ("Notification" in window) {
                updateStatusDisplay();
            } else {
                document.getElementById('status-display').textContent = 'Notifications unsupported by browser.';
                document.getElementById('status-display').classList.add('bg-red-100', 'text-red-700');
                document.getElementById('show-native-btn').disabled = true;
                document.getElementById('request-permission-btn').disabled = true;
            }
        }
    </script>
</body>
</html>

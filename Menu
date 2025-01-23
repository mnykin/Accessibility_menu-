<!DOCTYPE html>
<html>
<head>
    <link rel="stylesheet" href="https://fonts.googleapis.com/css2?family=Material+Symbols+Outlined:opsz,wght,FILL,GRAD@24,400,0,0&icon_names=settings_accessibility" />
    <link rel="stylesheet" href="https://fonts.googleapis.com/css2?family=Varela+Round&display=swap" />
    <style>
        #accessibilityToggle {
            position: fixed;
            bottom: 20px;
            left: 20px;
            background-color: #028C55;
            color: white;
            padding: 10px 20px;
            border: 2px solid white;
            border-radius: 8px;
            cursor: pointer;
            font-size: 16px;
            text-align: center;
            z-index: 9999;
        }
        #accessibilityBar {
            position: fixed;
            bottom: 70px;
            left: 20px;
            background-color: #028C55;
            padding: 10px;
            border-radius: 8px;
            display: flex;
            flex-wrap: wrap;
            margin: 5px;
            z-index: 9999;
        }
        .accessibilityButton {
            background-color: white;
            color: #028C55;
            border: none;
            border-radius: 5px;
            padding: 10px;
            font-size: 14px;
            font-family: 'Varela Round', sans-serif;
            cursor: pointer;
            text-align: center;
            flex: 1 1 120px;
            margin: 5px;
            transition: background-color 0.3s, color 0.3s;
        }
        .accessibilityButton.active {
            background-color: #FF5555;
            color: white;
        }
        .accessibilityButton:hover {
            background-color: #FF5555;
            color: white;
        }
        #accessibilityStatementText {
            position: fixed;
            bottom: 10px; 
            left: 50%;
            transform: translateX(-50%);
            background-color: white; 
            text-align: center;
            cursor: pointer;
            color: #028C55;
            font-family: 'Varela Round', sans-serif;
            text-decoration: underline;
            font-size: 26px;
            z-index: 9999;
            padding: 5px 10px;
            border-radius: 5px;
            display: none;
        }
    </style>
</head>
<body>
    <script>
    document.addEventListener("DOMContentLoaded", function () {
        // Debounce function to limit rapid executions
        function debounce(func, delay) {
            let timeoutId;
            return function (...args) {
                clearTimeout(timeoutId);
                timeoutId = setTimeout(() => func.apply(this, args), delay);
            };
        }

        // Configuration object for easier maintenance
        const CONFIG = {
            excludedElements: ['accessibilityBar', 'accessibilityToggle', 'accessibilityStatementText', 'galleryContainer'],
            minFontSize: 10,
            maxFontSize: 32,
            minZoom: 0.75,
            maxZoom: 1.25,
            zoomStep: 0.1
        };

        // Create content wrapper to isolate accessibility modifications
        function createContentWrapper() {
            const contentWrapper = document.createElement('div');
            contentWrapper.id = "contentWrapper";
            contentWrapper.style.cssText = "position: relative; overflow: auto; height: 100%; width: 100%;";

            // Move only child nodes that are not part of the excluded elements
            Array.from(document.body.children)
                .filter(child => !CONFIG.excludedElements.includes(child.id))
                .forEach(child => contentWrapper.appendChild(child));

            document.body.appendChild(contentWrapper);
            return contentWrapper;
        }

        // Optimize font size and zoom tracking
        const fontSizeTracker = {
            originalSizes: new WeakMap(),
            storeSizes() {
                document.querySelectorAll('*').forEach(el => {
                    this.originalSizes.set(el, window.getComputedStyle(el).fontSize);
                });
            },
            reset(contentWrapper) {
                this.originalSizes.forEach((size, el) => {
                    if (!el.closest('.accessibilityBar')) {
                        el.style.fontSize = size;
                    }
                });
                contentWrapper.style.transform = 'scale(1)';
                contentWrapper.dataset.zoomLevel = 1;
            }
        };

        // Accessibility features
        const AccessibilityFeatures = {
            activeFeatures: {},
            
            toggleFeature(feature, action) {
                const button = document.getElementById(feature);
                const isActive = this.activeFeatures[feature];
                
                action(!isActive);
                this.activeFeatures[feature] = !isActive;
                button.classList.toggle("active");
            },

            disableFlashes(enable) {
                const styleId = "disableFlashesStyle";
                const existingStyle = document.getElementById(styleId);
                
                if (enable && !existingStyle) {
                    const style = document.createElement('style');
                    style.id = styleId;
                    style.innerHTML = `
                        * {
                            animation: none !important;
                            transition: none !important;
                        }
                        video, iframe {
                            display: none !important;
                        }
                    `;
                    document.head.appendChild(style);
                } else if (!enable && existingStyle) {
                    existingStyle.remove();
                }
            },

            keyboardNavigation(enable) {
                const styleId = "keyboardNavStyle";
                const existingStyle = document.getElementById(styleId);
                
                if (enable && !existingStyle) {
                    const style = document.createElement('style');
                    style.id = styleId;
                    style.innerHTML = `
                        *:focus {
                            outline: 2px dashed #FF5555 !important;
                            outline-offset: 2px !important;
                        }
                    `;
                    document.head.appendChild(style);

                    document.querySelectorAll('a, button, input, textarea, select').forEach(el => {
                        el.setAttribute('tabindex', '0');
                    });
                } else if (!enable && existingStyle) {
                    existingStyle.remove();
                    document.querySelectorAll('[tabindex]').forEach(el => {
                        el.removeAttribute('tabindex');
                    });
                }
            },

            toggleFilter(filterValue, contentWrapper) {
                const currentFilter = contentWrapper.style.filter;
                contentWrapper.style.filter = currentFilter === filterValue ? 'none' : filterValue;
            },

            toggleFontFamily(enable) {
                document.querySelectorAll('*').forEach(el => {
                    if (!el.closest('.accessibilityBar') && el.id !== "accessibilityToggle" && !el.classList.contains("material-symbols-outlined")) {
                        el.style.fontFamily = enable ? 'Arial, sans-serif' : "'Varela Round', sans-serif";
                    }
                });
            },

            adjustFontSize(contentWrapper, step) {
                document.querySelectorAll('*').forEach(el => {
                    if (!el.closest('.accessibilityBar')) {
                        const currentSize = parseFloat(window.getComputedStyle(el).fontSize);
                        const newSize = currentSize + step;
                        if (newSize >= CONFIG.minFontSize && newSize <= CONFIG.maxFontSize) {
                            el.style.fontSize = `${newSize}px`;
                        }
                    }
                });
            },

            adjustZoom(contentWrapper, step) {
                let currentZoom = parseFloat(contentWrapper.dataset.zoomLevel) || 1;
                const newZoom = parseFloat((currentZoom + step).toFixed(2));

                if (newZoom >= CONFIG.minZoom && newZoom <= CONFIG.maxZoom) {
                    contentWrapper.style.transform = `scale(${newZoom})`;
                    contentWrapper.dataset.zoomLevel = newZoom;
                }
            },

            resetAll(contentWrapper) {
                // Reset all features and styles
                contentWrapper.style.filter = 'none';
                fontSizeTracker.reset(contentWrapper);

                document.querySelectorAll('*').forEach(el => {
                    if (!el.closest('.accessibilityBar') && el.id !== "accessibilityToggle") {
                        el.style.fontFamily = "'Varela Round', sans-serif";
                    }
                });

                document.querySelectorAll('h1, h2, h3, a').forEach(el => el.style.outline = 'none');

                Object.keys(this.activeFeatures).forEach(feature => {
                    this.activeFeatures[feature] = false;
                    document.getElementById(feature)?.classList.remove("active");
                });

                const accessibilityToggle = document.getElementById("accessibilityToggle");
                if (accessibilityToggle) {
                    accessibilityToggle.innerHTML = `<span class="material-symbols-outlined">settings_accessibility</span>`;
                }
            },

            toggleOutline(selector, style) {
                const elements = document.querySelectorAll(selector);
                const isCurrentlyOutlined = elements[0] && elements[0].style.outline !== 'none';
                
                elements.forEach(el => {
                    el.style.outline = isCurrentlyOutlined ? 'none' : style;
                });
            }
        };

        // Lazy initialization to minimize initial page load impact
        setTimeout(() => {
            const contentWrapper = createContentWrapper();
            fontSizeTracker.storeSizes();

            const accessibilityToggle = document.createElement('button');
            accessibilityToggle.id = "accessibilityToggle";
            accessibilityToggle.innerHTML = `<span class="material-symbols-outlined">settings_accessibility</span>`;
            document.body.appendChild(accessibilityToggle);

            const accessibilityBar = document.createElement('div');
            accessibilityBar.id = "accessibilityBar";
            accessibilityBar.classList.add('accessibilityBar');
            accessibilityBar.style.display = "none";
            accessibilityBar.style.border = "4px solid white";

            const options = [
                { id: 'keyboardNav', label: 'ניווט מקלדת', shortcut: 'Shift+A' },
                { id: 'disableFlashes', label: 'ביטול הבהובים', shortcut: 'Shift+B' },
                { id: 'monochrome', label: 'מונוכרום', shortcut: 'Shift+C' },
                { id: 'sepia', label: 'ספיה', shortcut: 'Shift+D' },
                { id: 'highContrast', label: 'ניגודיות גבוהה', shortcut: 'Shift:E' },
                { id: 'blackYellow', label: 'שחור צהוב', shortcut: 'Shift+F' },
                { id: 'invertColors', label: 'היפוך צבעים', shortcut: 'Shift+G' },
                { id: 'highlightHeadings', label: 'הדגשת כותרות', shortcut: 'Shift+H' },
                { id: 'highlightLinks', label: 'הדגשת קישורים', shortcut: 'Shift+I' },
                { id: 'readableFont', label: 'גופן קריא', shortcut: 'Shift+L' },
                { id: 'increaseFont', label: 'הגדלת גופן +', shortcut: 'Shift+M' },
                { id: 'decreaseFont', label: 'הקטנת גופן -', shortcut: 'Shift+N' },
                { id: 'zoomIn', label: 'הגדלת מסך +', shortcut: 'Shift+P' },
                { id: 'zoomOut', label: 'הקטנת מסך -', shortcut: 'Shift+Q' },
                { id: 'resetSettings', label: 'X איפוס הגדרות', shortcut: '', color: '#FF5555' },
            ];

            const functions = {
                keyboardNav: () => AccessibilityFeatures.toggleFeature('keyboardNav', AccessibilityFeatures.keyboardNavigation),
                disableFlashes: () => AccessibilityFeatures.toggleFeature('disableFlashes', AccessibilityFeatures.disableFlashes),
                monochrome: () => AccessibilityFeatures.toggleFeature('monochrome', (enable) => 
                    AccessibilityFeatures.toggleFilter('grayscale(100%)', contentWrapper)),
                sepia: () => AccessibilityFeatures.toggleFeature('sepia', (enable) => 
                    AccessibilityFeatures.toggleFilter('sepia(100%)', contentWrapper)),
                highContrast: () => AccessibilityFeatures.toggleFeature('highContrast', (enable) => 
                    AccessibilityFeatures.toggleFilter('contrast(200%)', contentWrapper)),
                blackYellow: () => AccessibilityFeatures.toggleFeature('blackYellow', (enable) => 
                    AccessibilityFeatures.toggleFilter('invert(1) hue-rotate(90deg)', contentWrapper)),
                invertColors: () => AccessibilityFeatures.toggleFeature('invertColors', (enable) => 
                    AccessibilityFeatures.toggleFilter('invert(100%)', contentWrapper)),
                highlightHeadings: () => AccessibilityFeatures.toggleOutline('h1, h2, h3', '2px solid red'),
                highlightLinks: () => AccessibilityFeatures.toggleOutline('a', '2px dashed blue'),
                readableFont: () => AccessibilityFeatures.toggleFeature('readableFont', AccessibilityFeatures.toggleFontFamily),
                increaseFont: () => AccessibilityFeatures.adjustFontSize(contentWrapper, 2),
                decreaseFont: () => AccessibilityFeatures.adjustFontSize(contentWrapper, -2),
                zoomIn: () => AccessibilityFeatures.adjustZoom(contentWrapper, 0.1),
                zoomOut: () => AccessibilityFeatures.adjustZoom(contentWrapper, -0.1),
                resetSettings: () => AccessibilityFeatures.resetAll(contentWrapper)
            };

            options.forEach(option => {
                const button = document.createElement('button');
                button.id = option.id;
                button.classList.add("accessibilityButton");
                button.innerHTML = `
                    <div>${option.label}</div>
                    <small>${option.shortcut}</small>
                `;
                if (option.color) {
                    button.style.backgroundColor = option.color;
                }
                button.addEventListener('click', functions[option.id]);
                accessibilityBar.appendChild(button);
            });

            document.body.appendChild(accessibilityBar);

            // Accessibility Statement Text
            const accessibilityStatementText = document.createElement('div');
            accessibilityStatementText.id = "accessibilityStatementText";
            accessibilityStatementText.innerHTML = "הצהרת נגישות";
            accessibilityStatementText.addEventListener('click', function () {
                const modal = document.createElement('div');
                modal.style.cssText = `
                    position: fixed;
                    top: 50%;
                    left: 50%;
                    transform: translate(-50%, -50%);
                    background-color: white;
                    padding: 20px;
                    border-radius: 8px;
                    box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
                    z-index: 10000;
                    text-align: center;
                    width: 300px;
                `;
                modal.innerHTML = `
                    <h2 style="font-size: 32px;">הצהרת נגישות</h2>
                    <p style="font-size: 24px; line-height: 1.6;">אנחנו מחויבים להבטיח חוויית גלישה נגישה ונוחה לכלל האוכלוסייה</p>
                    <p style="font-size: 20px; line-height: 1.8;">לפרטים נוספים מוזמנים לפנות אלינו:</p>
                    <p style="font-size: 20px; line-height: 1.8; direction: ltr;">ori@kobyran.co.il</p>
                    <p style="font-size: 20px; line-height: 1.8;">או בטלפון:</p>
                    <p style="font-size: 20px; line-height: 1.8; direction: ltr;">03-6132025</p>
                    <button id="closeModal" style="
                        background-color: #028C55; 
                        color: white; 
                        border: none; 
                        padding: 10px 20px; 
                        border-radius: 5px; 
                        cursor: pointer;
                        margin-top: 10px;">סגור</button>
                `;
                document.body.appendChild(modal);

                document.getElementById('closeModal').addEventListener('click', function () {
                    document.body.removeChild(modal);
                });
            });
            document.body.appendChild(accessibilityStatementText);

            accessibilityToggle.addEventListener('click', function () {
                const isVisible = accessibilityBar.style.display === "block";
                accessibilityBar.style.display = isVisible ? "none" : "block";
                accessibilityStatementText.style.display = isVisible ? "none" : "block";
            });

            // Add global click handler for link navigation
            document.addEventListener('click', function (event) {
                const target = event.target.closest('a');
                if (target && target.getAttribute('href') && !target.getAttribute('href').startsWith('#')) {
                    window.location.href = target.getAttribute('href');
                }
            });
        }, 1500); // Delay initialization
    });
    </script>
</body>
</html>

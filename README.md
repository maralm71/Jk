# Jk
<!DOCTYPE html>
<html lang="fa">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>فال حافظ با تشخیص چهره</title>
    <script defer src="https://cdn.jsdelivr.net/npm/face-api.js"></script>
    <style>
        body { text-align: center; font-family: Arial, sans-serif; background-color: black; color: white; }
        video { width: 100%; height: auto; }
        #fal-text { margin-top: 20px; font-size: 20px; background: rgba(0, 0, 0, 0.7); padding: 15px; border-radius: 10px; }
    </style>
</head>
<body>

    <h2>📷 لبخند بزنید تا فال شما نمایش داده شود!</h2>
    <video id="video" autoplay playsinline></video>
    <div id="fal-text">منتظر تشخیص چهره...</div>

    <script>
        const falList = {
            happy: ["آینده‌ای روشن در انتظار توست!", "شادی در مسیر زندگی‌ات جریان دارد!", "موفقیت نزدیک است!"],
            sad: ["غم‌ها می‌گذرند، امید داشته باش.", "صبوری کن، روزهای خوب می‌آیند.", "هیچ سختی‌ای همیشگی نیست."],
            neutral: ["آرامش بزرگترین نعمت است.", "از لحظات ساده‌ی زندگی لذت ببر.", "مسیر تو روشن است!"],
            surprised: ["یک خبر خوب در راه است!", "زندگی همیشه شگفتی دارد!", "آماده‌ی اتفاقات خوب باش!"],
            angry: ["آرامش را انتخاب کن.", "صبوری، کلید موفقیت است.", "هر مشکلی راه‌حلی دارد."],
            fearful: ["نگرانی بیهوده است، آینده روشن است.", "شجاعت درون توست!", "ترس را کنار بگذار، تو قوی هستی!"]
        };

        async function startApp() {
            const video = document.getElementById('video');
            const falText = document.getElementById('fal-text');

            // دریافت دسترسی به دوربین
            try {
                const stream = await navigator.mediaDevices.getUserMedia({ video: true });
                video.srcObject = stream;
            } catch (error) {
                falText.innerText = "⛔ دسترسی به دوربین مسدود شده است!";
                return;
            }

            // بارگذاری مدل‌ها از پوشه models/
            await faceapi.nets.tinyFaceDetector.loadFromUri('models/');
            await faceapi.nets.faceExpressionNet.loadFromUri('models/');

            setInterval(async () => {
                const detections = await faceapi.detectAllFaces(video, new faceapi.TinyFaceDetectorOptions()).withFaceExpressions();

                if (detections.length > 0) {
                    const expressions = detections[0].expressions;
                    let mood = "neutral";

                    if (expressions.happy > 0.5) mood = "happy";
                    else if (expressions.sad > 0.5) mood = "sad";
                    else if (expressions.surprised > 0.5) mood = "surprised";
                    else if (expressions.angry > 0.5) mood = "angry";
                    else if (expressions.fearful > 0.5) mood = "fearful";

                    let selectedFal = falList[mood][Math.floor(Math.random() * falList[mood].length)];
                    falText.innerText = "✨ فال شما: " + selectedFal;
                    speakText(selectedFal);
                }
            }, 5000);
        }

        function speakText(text) {
            let msg = new SpeechSynthesisUtterance();
            msg.text = text;
            msg.lang = "fa-IR";
            msg.rate = 0.9;
            speechSynthesis.speak(msg);
        }

        startApp();
    </script>

</body>
</html>

# thesecondbest93.github.io
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Microphone Noise Level Bar</title>
    <style>
        body {
            text-align: center;
        }

        #title {
            font-size: 24px;
            font-weight: bold;
            margin-top: 20px;
        }

        #bar {
            width: 80%;
            height: 30px;
            background-color: lightgray;
            position: relative;
            margin: 50px auto;
            overflow: hidden;
        }

        #fill {
            height: 100%;
            background-color: green;
            position: absolute;
            top: 0;
            left: 0;
            transition: width 0.5s;
        }

        #percentage {
            position: absolute;
            top: 50%;
            left: 0;
            width: 100%;
            transform: translateY(-50%);
            color: white;
            font-weight: bold;
        }
    </style>
</head>

<body>
    <div id="title">Yap-o-Meter Sponsored by The Teresa Moore Blog 2.0</div>
    <div id="bar">
        <div id="fill"></div>
        <div id="percentage">0%</div>
    </div>

    <script>
        window.addEventListener('DOMContentLoaded', () => {
            const bar = document.getElementById('bar');
            const fill = document.getElementById('fill');
            const percentage = document.getElementById('percentage');

            navigator.mediaDevices.getUserMedia({ audio: true })
                .then(stream => {
                    const audioContext = new AudioContext();
                    const analyzer = audioContext.createAnalyser();
                    const microphone = audioContext.createMediaStreamSource(stream);
                    microphone.connect(analyzer);

                    analyzer.fftSize = 256;
                    const bufferLength = analyzer.frequencyBinCount;
                    const dataArray = new Uint8Array(bufferLength);

                    const update = () => {
                        analyzer.getByteFrequencyData(dataArray);
                        const average = dataArray.reduce((a, b) => a + b, 0) / bufferLength;
                        const percent = average / 2.55; // Map average to percentage (0-100)
                        fill.style.width = `${percent}%`;

                        if (percent < 35) {
                            fill.style.backgroundColor = 'green';
                        } else if (percent < 70) {
                            fill.style.backgroundColor = 'yellow';
                        } else {
                            fill.style.backgroundColor = 'red';
                        }

                        percentage.textContent = `${percent.toFixed(0)}%`;
                        requestAnimationFrame(update);
                    };
                    update();
                })
                .catch(err => {
                    console.error('Error accessing microphone:', err);
                });
        });
    </script>
</body>

</html>

Hydration and indoor CO₂ concentration have a lot more impact on my cognition than I expected.

At home, I often found it hard to focus, felt dizzy, tired, or generally less sharp. I would find myself with very low energy and having trouble getting up from our couch or the armchair. This was terrible for my productivity and mood, I had to fix it.

After some research and a meeting with a doctor, I identified four possible causes: insufficient hydration, iron deficiency, sleep apnea, and high CO₂ concentration in the room. I now believe it was both too much CO₂ accumulating in the room and me not drinking enough water.

**Water**. I learned from my doctor that I need to drink at least 1.5 litres of water per day. We had small glasses at home, so I was below that. Now, I have a 1.5-litre bottle that I fill up every day, sometimes more, and I have this problem much less.

It's easy for me to test quickly if I have been drinking enough. Can I drink one glass of water in one go, very fast, and still want more? If yes, I was insufficiently hydrated. Regularly testing this helps to get feedback on what I've been doing wrong and train my brain to drink more.

![A picture of my new bottle next to my small glasses.](https://raw.githubusercontent.com/ddorn/blog/main/co2/bottle_and_glass.png)
*Size matters.*

**CO₂ concentration**. CO₂ concentration in the air outside is [about 430 ppm](https://gml.noaa.gov/ccgg/trends/), parts per million, equivalent to 0.043% of the air. It can be slightly higher in cities and is generally rising because of human emissions, which is an important factor for climate change. This is, however, not our topic, as human health and cognition are mostly not affected up to 1000 ppm of CO₂ in the air, which doesn't happen outdoors.

Indoors, CO₂ is mostly produced by breathing and is trapped in rooms if they are poorly ventilated. Thus, CO₂ concentration can reach high levels if there are more people in the room, if the room is fuller, if people stay in the same room for longer or if ventilation is worse. This can happen easily in meeting rooms, classrooms, cars, bedrooms, and, apparently, in our flat.

CO₂ concentrations start to have negative effects at 1000 ppm, which increase in intensity after that. In practice, when there are two of us in our flat, it takes 2–3 hours to reach 1000 ppm. When it reaches 1500 (on our CO₂ meter), we have little energy, until we open the windows. Once we get some fresh air, our energy comes back in a matter of minutes, consistently, and we wonder why we've been doing nothing for the past 30 minutes.

**CO₂ monitoring**. Knowing if I feel bad because of the CO₂ is slightly trickier than with water. Getting better after opening the windows is a good sign, but could be for many other reasons than CO₂, and could be a placebo effect. So, to be able to prevent feeling bad, I decided to make a CO₂ monitor that alerts us by flashing our lights when the concentration of CO₂ rises above 1000 ppm. I didn't know if I could trust cheap commercial options, so I directly ordered the sensor used by [AirGradient](https://www.airgradient.com/), a cool open hardware initiative to improve air quality (but too expensive for me), and plugged it into an old Raspberry Pi.

![My old Raspberry Pi in a rainbow case with the Sensair S8 attached.](https://raw.githubusercontent.com/ddorn/blog/main/co2/raspi_and_co2.png)
*My old Raspberry Pi in a rainbow case with the Sensair S8 attached.*

This was easier than expected (in particular, thanks to Marc's help). After receiving the [Sensair S8](https://senseair.com/product/s8/), we soldered pin headers on it, then I connected it with jump wires to the [serial port](https://en.wikipedia.org/wiki/Serial_port) of the Raspberry Pi, and then adapted [a simple python script](https://github.com/ndoornekamp/senseair_s8) to read from it every five minutes. Then I could directly send a command to blink my Hue lightbulbs, but I decided to set up [Home Assistant](https://www.home-assistant.io/) instead. This way, I can also have a history of CO₂ concentration and get notifications on my phone when the CO₂ is too high (which is great when the lights are off and can't blink).

The first time my lights blinked, once, twice, three times, was at dinner. For a second, I was confused: I had already forgotten that I plugged the monitor in the morning. Somehow, I was surprised to find myself with low energy for the past ~15 minutes, and quite grateful for this little device I made which was able to notice it and snap me out of it! It's nice to know that the little Pi has my back.

For the next days, every time it blinked, I found myself in the same low energy situation. It's nice to notice it, but I wanted to prevent it, so I lowered the threshold for my lights to blink from 1400 ppm to 1000 ppm, and I can now prevent the situation altogether!

**Science of CO₂ and cognitive function**. I'm strongly in favour of self experimentation for personal matters, but scientists often have something interesting to say. The most cited papers ([Allen 2015](https://www.semanticscholar.org/paper/Associations-of-Cognitive-Function-Scores-with-and-Allen-MacNaughton/6043a89a58f705f1903012dadd4a59155e4be1bc), [Satish 2012](https://www.semanticscholar.org/paper/Is-CO2-an-Indoor-Pollutant-Direct-Effects-of-CO2-on-Satish-Mendell/309e7ebea6e880cd2393bc38596933813022d335)) and reviews ([Du 2020](https://www.semanticscholar.org/paper/Indoor-CO2-Concentrations-and-Cognitive-Function%3A-A-Du-Tandoc/76e8b7239ca50d4fdea265c88b16e9a5c1e4b23c)) find a decrease in cognitive ability above 1000 ppm, especially for higher level cognitive function and decision-making. For instance, [Allen 2015](https://www.semanticscholar.org/paper/Associations-of-Cognitive-Function-Scores-with-and-Allen-MacNaughton/6043a89a58f705f1903012dadd4a59155e4be1bc) finds a decrease of 15-50% of performance on standard cognitive function tests at 1400 ppm, depending on the domain, the highest loss being for "Strategy" and "Information usage". I could not find any rebuttal or disagreement with those studies, and they surprised me in their strength.

**Measuring CO₂ concentration**. I was still confused about how we actually measure CO₂ concentration. It turned out, the core idea is simple, and we've seen it in our high school physics class. I had just forgotten it. If you want to figure it out for yourself, now is the time to pause and ponder.


<details>
<summary>Solution: how does a CO₂ monitor work? (click to expand)</summary>

Modern CO₂ meters use non-dispersive infrared light and are made of three main components:

* An infrared light, that emits light at the 4.26-micron wavelength, which CO₂ molecules uniquely absorb
* A chamber filled with outside air
* An infrared detector behind a 4.26-micron wavelength filter

On one side of the chamber, infrared light is emitted, and on the other side, the infrared detector measures how much light has been absorbed by the CO₂. Then, using [Beer-Lambert's law](https://en.wikipedia.org/wiki/Beer%E2%80%93Lambert_law), we can convert the light attenuation into a concentration! For a deeper dive, Forensic Detectors have [a great article on it](https://www.forensicsdetectors.com/blogs/articles/how-does-an-ndir-co2-sensor-work).

![Schema of a CO₂ monitor, taken from the Forensic Detectors' article.](https://raw.githubusercontent.com/ddorn/blog/main/co2/co2_monitor_schema.png)
*Schema of a CO₂ monitor, taken from the Forensic Detectors' article.*

</details>

**Is it CO₂?** I'm still unsure if CO₂ is the sole cause of what we were experiencing, or just a factor that made the mix worse (with at least poor hydration, and poor ventilation is more than just about CO₂). In particular, if my sensor is well calibrated, the CO₂ levels I'm talking about are quite common in offices and other buildings, without creating such a strong reaction. It might also be that some people (me?) have a higher sensitivity to indoor CO₂. The other times when I've felt something similar are after long video calls in call boxes or meetings in small rooms. Maybe I was tired afterwards because of poor ventilation, and not because the speaker was supernaturally boring.

What is certain is that I don't get as tired anymore when I take care of those two variables.

*Stay hydrated, stay ventilated.*

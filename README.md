# Bernd-der-Buffer
This is a story about Bernd. Bernd is a buffer trapped inside the feedback loop of a precision amplifier, doomed to spend the rest of his days delivering and sinking current.

# Introduction: Buffering and Composite Amplifiers

Sometimes we have an OpAmp that perfectly fits our needs, but we need it to drive a load it can't drive by itself. How do we still manage to maintain the characteristics of the OpAmp that made us choose it in the first place while improving it's capability to drive a variety of loads? Or maybe we want even more loop gain than one OpAmp can provide us with, so what now? Composite Amplifiers to the rescue! The concept of composite amplifiers is nothing new and has been around for decades and so has been literature on it. I will list my favourite articles, books and authors here. If you aren't familiar with the concept, make sure to at least read some of the application notes. SBOA357 from TI is probably the shortest summary you can get.

App Notes:

TI/BB - SBOA015 - Feedback plots define OpAmp AC Performance 

TI/BB - SBOA002 - Classical OpAmp or Current Feedback OpAmp? - This composite OpAmp gives you the best of both worlds

TI - SBOA357 - Designing High-Precision, High Output Current Circuits for ATE Applications Using a Composite Amplifier Loop

AD/LT - AN21 - Composite Amplifiers

Books:

OpAmp Applications Handbook by Walt Jung (Analog Devices)

Authors:

Sergio Franco

Walt Jung

# Different ways to buffer

When it comes to implementations of composite amplifiers where the target isn't so much increased loop gain as it is increased output drive, there are a few different routes. In the following, I will assume that the topology is a composite loop with a VFA as the precision/control amplifier.

Option 1: Use a VFA

We can of course use another voltage feedback amplifier in the loop, connected in a non-inverting configuration with or without gain. There are thousands of VFAs out there and a candidate with very respectable output drive capabilities would be AD8397. Sadly it can only be operated up to +/-12V rails. Another option with great AC as well as DC specs and relatively high output current is OPA2156/OPA1656. A drawback is that these VFAs usually don't come close to CFAs regarding bandwidth and slew rate. Especially the bandwith aspect can be tricky.

Option 2: Use a CFA

A common approach is to build a VFA-CFA Composite amplifier with a CFA wrapped into the loop of the VFA. These CFAs have (thanks to their topology) greater slew rates and bandwith. They are commonly used as line drivers and thus there exists a selection of CFAs that are specified to drive upwards of 100mA into 50 Ohm loads. When searching for these, you will not always find them under OpAmps, sometimes they are in special categories for Line Drivers (Video or DSL). One good candidate is THS6012, also available as TPA6120A2 (for a lot less). It is quite a beefy CFA line driver, with very respectable bandwidth and AC specs. Other options for more money and with less output current would be THS3062, AD811 and a bunch of other line drivers. The maximum supply range for all these is +/-15V. Another kind of beast is the ADA4870. It's a chunky package and can source or sink up to 1A from/to +/-20V supplies. It requires very good heatsinking, hasn't got tons of bandwidth (relatively speaking), is expensive and not the easiest to get a tight layout with.

Option 3: Use an Open Loop Buffer

Most of these buffers are of the "diamond" variety. It's a structure first described in a 1963 IEEE Solid State Paper by R. Baker from MIT and patented by ComLinear as the "Current mode feedback circuit". Basically it's two complementary emitter followers in series. This concept is also the basis of the current feedback amplifiers in general. There are a bunch of Open Loop Buffers based on this topology:

BUF634(A)
LMH6321
LME49600

Their AC specs are usually nothing to write home about, but they all can sink/source in the neighboorhood 250mA and at least the BUF634 and LME49600 run on +/-18V supplies. They don't have any loop gain by themselves, so their AC specs will usually fall short of VFAs and CFAs configured as noninverting amplifiers. Thus, we have to rely on the loop gain the precision OpAmp can provide. In this configuration it is even more important that our precision/control OpAmp has lots of open loop gain.

Option 4: Build a discrete Buffer to our needs

There are a few reasons why one would want to use discrete components, but there are also obvious shortcomings. First off, if you want more current, you can always parallel ICs. It's a common approach, it's easy and it usually works quite well. If higher voltage swing is needed, you would have to do something like bootstrapping the supply rails of the control opamp and I really don't know enough about this kind of stuff to be writing about it. On the other hand, you might want to tailor the buffer to your specific needs. All of the open loop buffers have Class AB Push Pull output stages (the diamond structure). Like any Class AB output stage, they will operate in Class A for very low currents. If you look at their quiescent current consumption you can get a rough idea about how much or how little current they can probably drive while staying in Class A. Why would we want to stay in Class A? Crossover distortion would be the obvious answer, but it is also the half-wave rectified currents these buffers will draw from the supply lines when they leave Class A. With a discrete design, we can set the bias point accordingly, so the operates in Class A under usual operating conditions. Of course we have to know what those operating conditions are in advance. If we have to drive higher currents, we might have to modify our circuit in order to maintain as much linearity as possible. In general, I think we should try to apply the KISS principle, because a more minimalistic circuit will lead to a tighter layout, better thermal tracking of the devices and less variables overall. We still won't achieve bandwiths near that of IC implementations and their thermal tracking and DC offset will likely be superior. So we have to deal with a significantly slower buffer. How much slower you ask? Well I don't know yet. Thats where this project stands right now.

# Current Status: Evaluation

I am currently in the process of evaluating different open loop buffer topologies and implementations for their AC specs. DC offset isn't that much of a concern to me, the open loop gain of the OpAmp will take care of that. The route I decided to go is to:

1. Figure out how much bandwidth I can potentially achieve on real world through hole and SMD implementations using common transistors.
2. Incorporate the findings into updated versions, now focussing on AC performance in a broader scope.
3. to be continued...

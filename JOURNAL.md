---
title: "Discrete Logic Pong"
author: "jeevan hatti"
description: "A discrete-logic Pong implementation using 74xx-style ICs!"
created_at: "2026-03-12"
---

# March 12, 10 PM: Research + Starting Simulation File

I kicked off this project by first researching the original implentation of pong. It was implemented with only discrete logic and ICs, with no microcontroller or firmware used at all. I reviewed the various subsystems of the schematic to get a better understanding of the things I would need to build. With current ICs, it should be doable with far less than 66 ICs that it was originally implemented with.

![image](https://stasis.hackclub-assets.com/images/1773353686386-z7o3ul.png)


After some reviewing I decided that starting on the display subsystem would be the wisest, and so I started researching how VGA works.

![image](https://stasis.hackclub-assets.com/images/1773353845049-ewvnop.png)
It consists of the RGB channels, VSYNC and HSYNC and a pixel clock. Seems relatively ok.

With that, I started working on the HSYNC signal to synchronise the screen, and a H Coordinate system using some binary counters. A lot of time was spent figuring out how to use Digital (the cool logic sim I'm using for this). Didn't  manage to get anythign working yet, but I was only at it for a bit.

The goal for the next major session is to get the VGA monitor component working.


![image](https://stasis.hackclub-assets.com/images/1773353591479-e5yc57.png)

**Total time spent: 1.42 hours**

# March 13, 8 AM: HSYNC Counter

Started the HSYNC counter.

![image](https://stasis.hackclub-assets.com/images/1773388978669-u0bpyk.png)

Since we're generating the coordinate system's x value, we need 640, so we need 10 bits. But on some further researching, apparently you have to leave 140 odd pixels for blanking (to prevent streaking or something). So we have to be able to count up to 800. Still takes 10 bits, so we still need 3 4-bit counters. I wired the three ICs up, set a clock of 25MHz, and for the reset logic, we're connected to bit 5, 8, 9, because the thats how 800 is expressed in binary. When those three hit 1, we pull clear to 0 (since its active low), and reset the counter. Very cool!

The VSYNC logic is much the same, just with different clear conditions. Will copy paste and implement that later on.


![image](https://stasis.hackclub-assets.com/images/1773386675658-vyg5c0.png)

**Total time spent: 0.5 hours**

# March 13, 10 AM: VSYNC

Just implemented the VSYNC Counter too. Same old same old, but I changed the reset logic to reset at 525, which is the standard resolution (480 + the 35 blanking pixels).


![image](https://stasis.hackclub-assets.com/images/1773395847305-rfkmnh.png)

The simulation seems to be handling it as expected, I might add a seven segment display later to verify that.


![image](https://stasis.hackclub-assets.com/images/1773395993110-qqihtq.png)

**Total time spent: 0.17 hours**

# March 13, 10 PM: Argh I don't like Digital

No I like digitial, but sure is it annoying to debug.


![image](https://stasis.hackclub-assets.com/images/1773441438492-w6je0o.png)

I started by cleaning up the wiring for the counters, and adding tunnels. THe majority of time was spent genrating the VSYNC and HSYNC Signals by using a latch to ensure the pin is high for whatever period it needs to.

The real pain came from the VGA compoenent. I cannot for the life of me get it to display an image. I'm sure there are bugs in my actual wiring sure, but it shouldn't prevent the software from actually displaying the VGA output thing, since it runs the simulation without throwing errors.

I also added some comparators to ensure that its in the actual drawing zone before trying to draw a pixel.


![image](https://stasis.hackclub-assets.com/images/1773441572748-p9l2ae.png)


Save me.

**Total time spent: 2 hours**

# March 14, 1 PM: Debugging the counters

Spent some time debugginig the counter to try and diagnose why my VGA isn't working. Realised I was using the wrong IC (yikes). I realised my reset logic was flawed, since it would reset at more values than just the intended one. Implemented a fix for that, but will come up with a btter solution later.


![image](https://stasis.hackclub-assets.com/images/1773494086195-qfvidh.png)

I'm still debugging my counter. I'm pretty sure my display logic is just bad right now, since I'm not handling the bits properly. Will fix that later.

Progress is quite slow, since I'm still pretty new to working with discrete logic and 74xx ICs.


![image](https://stasis.hackclub-assets.com/images/1773494143463-s5u364.png)

Also the Digital docs are pretty bad, the section on the seven segment display didn't even bother telling me which pins are which, so I assumed it was a typical 7 segment layout, but no, it organised them A-G alphabetically. Which ok fine is useful, but at least tell me that :(



![image](https://stasis.hackclub-assets.com/images/1773494215994-iwmqbf.png)

**Total time spent: 1 hours**

# March 14, 2 PM: Finally fixing the counter

I guess I am just incompetent. I tried using different coutner ICs for a bit, and rewiring everything again. Then I finally bit the bullet and started reading the datasheet for the 74162.


![image](https://stasis.hackclub-assets.com/images/1773497926062-xhby2y.png)


I realised the stupid mistake I had made, and rewired it in a new file, and now it actually counts. The issue before was the way I was doing the 3rd counter, now I fixed that by wiring the RCO from the previous one correctly, and now it actually counts!

I'll redo the reset logic, and start working towards the VGA again. I don't look forward to it not working.


![image](https://stasis.hackclub-assets.com/images/1773498001671-u8agpd.png)

**Total time spent: 0.5 hours**

# March 14, 6 PM: Finally Finally Fixing the Counter

I am incredibly stupid. I was using BCD. I don't even know why, I was literally using a seven segment and I didn't clock the fact that I was using BCD. The actual VGA monitor doesn't want BCD, but just pure binary.

The fix was relatively simple, I just converted the IC to the 74163 instead of the 162, which is a binary counter, not a BCD counter. I implemented that, the reset logic for both the Horizontal and Vertical Rows, and did some testing with an LED display for the binary bits, and a cool clock selector setup I made with some relays :]. Everything seems to finally be working!


![image](https://stasis.hackclub-assets.com/images/1773512317393-wz1ocf.png)



![image](https://stasis.hackclub-assets.com/images/1773512385558-fe4py6.png)




![image](https://stasis.hackclub-assets.com/images/1773512396555-nxcyco.png)

![image](https://stasis.hackclub-assets.com/images/1773512404191-lmtvpf.png)


Here are some pictures of me using the seven segment like a doofus (I deleted the parts where I implemented both the vertical and horizontal components because I just deleted them out of my file, this is the only screenshot I could salvage from my clipboard history :| )



![image](https://stasis.hackclub-assets.com/images/1773512475313-xwko5y.png)

I tried adding some images of the sim running, but alas Stasis didn't let me :(.

Ok now I can get backing to implementing the actual HSYNC and VSYNC Logic, getting this counter working too faaaaaar too long...

**Total time spent: 1.33 hours**

# March 14, 11 PM: WE ARE SCREENING!

THE SCREEN WORKS! IT WORKS! WHOO!

SEE I'M NOT DELUSIONAL:


![image](https://stasis.hackclub-assets.com/images/1773528753016-f1qezg.png)


Phew. So I took a break for a few hours, watched a nice little movie, and then got back to it. I basically just needed to generate the HSYNC and VSYNC signals, and we should have in theory had a working screen. If only it were that simple. Also if only I knew how to generate a SYNC signal.

Did a brief dive back into VGA theory, and came out knowing that I needed to pull the singals low between certain thresholds, namely between 28F and 2F0 (in hex, it translated the labels to hex, and I forgot the exact decimal values :]), and 1E8 and 1EB for HSYNC and VSYNC Respectively.


![image](https://stasis.hackclub-assets.com/images/1773528898978-x906lj.png)

I didn't want to implement it with actual 74xx chips just yet, just so I get a proof of theory before I commit to that, but hey ho, it works!

Should mention that VCOUNT and HCOUNT are just a splitter I used to put the number onto a singular bus:


![image](https://stasis.hackclub-assets.com/images/1773528946623-3ze0bn.png)

Of course, actually getting this to work was not as simple. I originally didn't realise it was active low, even though the error message was practically screaming at me. I did however catch the fact that my V Counter was resetting one count too late, and so I did fix that:


![image](https://stasis.hackclub-assets.com/images/1773529025399-t424ut.png)

My original implementation also was producing stupidly long HSYNC and VSYNC lengths. I suppose my comparator logic, or something with my buses was wrong. No one knows..

Also was feeling fancy, so I thought I'd flaunt the fact that I can colour my screen one of 3 entire colours(4 if you include white)!!



![image](https://stasis.hackclub-assets.com/images/1773529170903-jfme6r.png)

Really cool. I am very hyped.

Reading the last 6 or so hours of journals, I have realised that I do not operate well without caffeine. A lot of time was just spent doodling around with no real direction. I think I'll try and plan and research what I'm trying to do better and actually write it down, before I jump head first into placing it in the simulator. Literal hours spent switching between like 4 different ICs because I kept forgetting the difference between them, and being entirely confident that I didn't. Lesson learnt :].

For the next session, I'll probably start working on implementing it with actual ICs, so as to save future me (who will likely have forgotten whatever the hell this circuit is supposed to do), some pain of going through to find my caffeine riddled entry.

On another note, I should also work on hierarchical sheets, instead of producing everything on a singular sheet. Don't really know if its worth the hassle, Digital does have an infite canvas, and I like working like this.

GitHub will be setup soon though, I don't trust my self with not loosing the file.

**Total time spent: 1 hours**

# March 15, 9 AM: Converting RESET Logic to actual ICs

Spent some time finding a decent AND gate, and contemplating if I should use a triple 3 input or a quad 2 input. Ended up using a quad two input AND gate, the 7408.

I also started worrying about sourcing components, but after some digging around, I found that LCSC was my best bet in the UK, really cheap, huge library, and cheap delivery, everything Digikey isn't for me!

Also spent a while trying to find a DIP 25MHz oscillator. All of them seem to be SMD, but I did find something on Aliexpress that looks okay-ish:


![image](https://stasis.hackclub-assets.com/images/1773567810877-ljblif.png)

The start of what will be a very long BOM..

![image](https://stasis.hackclub-assets.com/images/1773567817398-te43ee.png)

Here's the actual wiring for the AND Gates for both HCLR and VCLR:

![image](https://stasis.hackclub-assets.com/images/1773567848688-6b47u4.png)

Ran it after implementing it, and it seems to work fine, which is new. Though of course there are very few ways in which you can screw up fanning an AND gate right..

**Total time spent: 0.67 hours**

# March 15, 10 AM: Converting SYNC Logic to actual ICs (pt1.2)

pt 1.2 because Stasis swallowed my original entry, so here we are rewriting it for the third time :(

So I started converting my VSYNC and HSYNC generation logic into actual 74xx ICs. Its just two comparators for each one, but since they are 10 bit, I need 3x4bit comparators cascading for each of the old comparators. That means I need 2x3x4bit for the VSYNC, and the same for HSYNC, totalling to 12 comparators... I could probably rework the logic, but honestly thats future me's problem, I want to get onto the actual game logic.

But yeah 12 comparators + 1 NAND since I don't want to get an invereter and reuse the old AND.


![image](https://stasis.hackclub-assets.com/images/1773570681886-f9vdaa.png)


![image](https://stasis.hackclub-assets.com/images/1773570685435-j8lxtb.png)


I also sourced the corresponding ICs off LCSC:


![image](https://stasis.hackclub-assets.com/images/1773570697226-89d3zw.png)

The current wiring doesn't quite work just yet, but I really need to focus on school now, so I'll probably fix it and get the HSYNC logic working sometime next week. Might have to put this project on hold if my other one finally gets approved.

**Total time spent: 0.5 hours**

# March 15, 11 PM: Converting SYNC Logic to actual ICs (pt2)

I fixed the problem with my 7485 VSYNC implementation. Turns out I was just doing the opposite inequalities since my bits were going into Q instead of P. Didn't bother switching the inputs around, just used the other output. That fixed VSYNC.

I copied over that module and replaced the bits and the coded constant with those for HSYNC, but for some reason its failing silently. Its just the HSYNC too. I tried running new VSYNC + new HSYNC, and no dice, but new VSYNC + old HSYNC works fine, so its definitely something wrong with the new HSYNC implementation. I'm pretty stuck, I'm not sure whats gone wrong, I've for sure coded in the right constants, I triple checked against my old comparator, but no dice. Pretty weird.

I guess I'll rewire it from scratch without copy pasting.


![image](https://stasis.hackclub-assets.com/images/1773617340263-tj31v8.png)

Also added switches to the old SYNC comparators to make debugging easier:


![image](https://stasis.hackclub-assets.com/images/1773617366403-nw4vgx.png)

**Total time spent: 0.5 hours**

# March 16, 8 PM: Converting SYNC Logic to actual ICs (pt3)

I finally fixed the HSYNC! I started by just tearing it down and rewiring it entirely for my sanity, but that still didn't work, and it was still failing silently, not fun. My first lead was that I kinda wired up the Comparator Cascade based on vibes, and I didn't really read any docs or even google an example.

Turns out you're not supposed to leave the input of the first Comparator floating, or not connected as I marked it. The thing is, I did exactly that for the VSYNC and it worked fine, so its not my fault...

Anyways once I discovered that, I just tied P=Q to VCC, and the other two inputs to GND, and voila, it worked!

Finally I can move onto actual game logic!

Here's a cool little mug of the replacement:

![image](https://stasis.hackclub-assets.com/images/1773693144189-n8ihqq.png)

Here's a little photo of the monitor doing its thing:


![image](https://stasis.hackclub-assets.com/images/1773693231748-2ycjpa.png)

I've kept the old SYNC logic in the file just in case. Will upload the new .dig to the GitHub soon. A thing of note, fanning out the 10 bit comparator IC into the 3 compoenent bits seems to have improved performance, not entirely sure how, but its running significantly faster. Of cause its just correlation, not causal just yet, but thought I'd mention it :]

Note after writing this journal entry:

I am stupid. Very stupid. What was I thinking use 12 comparators lol. The propagation delay is going to be crazy, I didn't really put it into perspective till after I got over my obsession of getting the screen working. Basically using 12 comparators is gonna throw the timing completely off.

The solution is so much more elegant. Just use a fricking s-r latch. That should significantly reduce the IC count. Basically set it when the bits match the lower porch pattern, then reset when it matches the upper, and then invert the output if needed. So much easier. I'll probably work on implementing that replacement soon. The end of this stupid VGA driver has not yet been reached...

**Total time spent: 0.5 hours**

# March 16, 10 PM: Replace the comparators with an S-R Latch

This was really quite painless somehow. Did some research into the ICs I could potentially use, and found an 8 Input NAND on LCSC. Sweet.

Using that, and a standard dual input NAND, I wired up my bit decoding, and wired that to the async set and reset inputs of a d type flip flop (D and CLK pulled low). And after adjusting the inequalities a tad, it was working just fine!


![image](https://stasis.hackclub-assets.com/images/1773698966488-x8jaus.png)


Not including NOTs, I've cut it down to just 7 ICs over the 13 ICs used in the other one, plus significantly lower propagation delay! Including some hex inverters, we're looking at like 4 extra ICs, so yes that brings us back to like 11 ICs, but again significanlty faster than using comparators, so fun!

I'll leave the not gates as is right now, instead of dropping in discrete hex inverter ICs, to keep some semblance of sanity. When I get a finished simulation, I'll probably convert everything to KiCad anyways, so I'll set that up there.

Here is the current file in its entire glory:

![image](https://stasis.hackclub-assets.com/images/1773699391698-75a9q6.png)

I took way to long to get here, but here we are...

**Total time spent: 0.75 hours**

# March 17, 9 PM: Weird fix

Not a substantial entry, but while I was just goofing around with the VGA Driver, I noticed in the SYNC generator, I was using a 7408, which is an AND. I coded in my bits assuming it was a NAND. This is definitely not the best explanation, but long story short, the cirucit was doing the right thing, but I don't know how. I could definitely get to the bottom of it by drawing up a truth table I guess, but I don't want to do that for 10 bits.


To confuse future me a little less, I chucked on a few NOTs onto the output of the NAND and the AND too. Still works suprisingly. I'm not entirely sure if my logic is sound, or if Digital is being a bit *too* forgiving. We'll find out when I build it I guess.


![image](https://stasis.hackclub-assets.com/images/1773782795132-oihul9.png)

![image](https://stasis.hackclub-assets.com/images/1773782894612-kgjmls.png)

**Total time spent: 0.08 hours**

# March 17, 10 PM: Drawing a line!

Read up a bit to make sure I completely understood how I actually draw stuff. Its pretty simple, you basically just setup a bunch of comparators (probably and gates in catual use, but for simplicity we'll gow with comps). When the x and y value is where you want the "pencil" to be, you set your colour high, and voila, we have stuff draw!

Here's what that looks like in practice:


![image](https://stasis.hackclub-assets.com/images/1773784309559-cp9vft.png)


![image](https://stasis.hackclub-assets.com/images/1773784319664-mtoc9u.png)

If you squint your eyse very closely, you'll see a single pixel wide blue streak in the exact centre of the screen.

Here's a more useful implementation of that:


![image](https://stasis.hackclub-assets.com/images/1773784373319-dvxqnd.png)


It draws the divider in the centre of the board like in the original Pong:


![image](https://stasis.hackclub-assets.com/images/1773784404843-8gptrd.png)

Albeit it is solid. You can fix that by adding another parameter for the vertical bit, and just ANDing horizontal and vertical parameters.

Here's a crude implementation of the same:

![image](https://stasis.hackclub-assets.com/images/1773784861933-l1rkh5.png)

As I learnt, I don't really want to use comparators since the propagation delay is excessive. Using NANDs is probably our best bet :]

Here's what that logic looks like:


![image](https://stasis.hackclub-assets.com/images/1773784911865-v65bgp.png)

Might want it a bit thicker. I'll try and design a resuable system to draw static things easier.

**Total time spent: 0.42 hours**

# March 18, 10 AM: Simplifying CLR Logic

I rewired the CLR logic from using just 7408s to using the cool 8 input NAND gate (74HC30). I only need 3 logic gates now (not including the obscene amount of hex inverters that I will need)!

Works pretty much the same, same inputs, NOT-ted the ouput of the NAND, and tied it to a central 7408 to get the remaining 2 bits and combine those two outputs and generate the clear signal.

Here's the new implementation:


![image](https://stasis.hackclub-assets.com/images/1773828115215-4spulx.png)

compared to the old:


![image](https://stasis.hackclub-assets.com/images/1773828135926-q9iuud.png)

**Total time spent: 0.33 hours**

# March 18, 12 PM: Discretifying the backdrop

Just spent some time converting the comparators from the crude implementation I made yesterday using comparators to using the cheeky 8 input NAND and the a single AND. Implemented both the Vertical and Horizontal checks with just two ICs

Here's the old implementation:


![image](https://stasis.hackclub-assets.com/images/1773837219035-xuvdxr.png)

Replaced by the very beautiful new implementation:


![image](https://stasis.hackclub-assets.com/images/1773837236519-xlb9tx.png)

I also realised I didn't acutally have to use as many Hex inverters as I thought, I can just make a bus of all the counter bits, and a bus with the inverted bits too, and that saves me having to use so many inverters. I only need inverers on the ouputs that I need to switch, which is fine. I'll figure out how to layout the buses when I get to that.

**Total time spent: 0.33 hours**

# March 21, 12 AM: Brainstorming Paddle Logic

Started working on implementing the two paddles. Using generic comparators, namely some comparators and a few ANDs and ORs. It should just be a simple range for the x values, but for the y, I'm not sure how I'll handle that just yet, since I have to add a certain amount (the paddle height) onto the y value, and it cant just be a constant like it is for the vertical. I don't know if I should use an adder, or if theres a simpler way to do this. Now that I write this, I suppose I could use another s-r latch and have that set for a period of however long we're counting using a simple counter. I'll work it out :]

I'm writing this journal intermittently during my work, since I think this will be a longer session. Heres the progress thus far:


![image](https://stasis.hackclub-assets.com/images/1773859066312-relowx.png)

![image](https://stasis.hackclub-assets.com/images/1773859074803-jjf2og.png)

I think the paddle dimensions are a bit too square right now...


![image](https://stasis.hackclub-assets.com/images/1773859568126-0pxx2n.png)


Thats better :]

With that proof of concept-ish thing working, I started designing the logic to actually let this move and not be static.

So uh, I think I broke something :]


![image](https://stasis.hackclub-assets.com/images/1774050338469-pjfs99.png)


IT WORKS:


![image](https://stasis.hackclub-assets.com/images/1774050466439-yhyav1.png)


I caved in and used an adder. It was either that or using a async D flip flop as an s-r + another 3ICs for the counter to get that to work. I traded out the counter for 3 4xbit adders, and it seems to work just fine.

I started working on getting the paddle to actually move. I messed around with the rotary encoder first, but that was a bit finicky so I thought I'd use a button first, but that didn't work entirely just yet. I will figure it out eventually, just not now, its like 1am...

Here's the current progress

![image](https://stasis.hackclub-assets.com/images/1774052829294-t953u1.png)

Not very much, but for the sake of completness, I also doodles some things :


![image](https://stasis.hackclub-assets.com/images/1774052859093-unpy1h.png)


![image](https://stasis.hackclub-assets.com/images/1774052867199-9t0hru.png)

**Total time spent: 2 hours**

# March 23, 8 PM: Y Counter Logic

I sat down and figure out my Y counter logic for the paddle :]. My doodling around last time with no real plan didn't really help, so I just sat down and thought about how I could implement it for longer than 20s.

My first doodle was this:


![image](https://stasis.hackclub-assets.com/images/1774295917868-4m3bk0.png)

Realised this would work fine with a counter with direction control and the ability to load a value, and so I got this:


![image](https://stasis.hackclub-assets.com/images/1774295941253-cd087a.png)


The arrows reprsent the buttons btw, idk why I did them as squares, but yes.

And I then fleshed out the system:

![image](https://stasis.hackclub-assets.com/images/1774295991305-v71nnd.png)

Haven't tied it to the screen just yet, but will work on that now. But the logic seems to do the clk and the direction just fine :]

**Total time spent: 0.5 hours**

# March 23, 9 PM: BOTH PADDLES GO BRR

Just copied over the logic, and modified it to work with a different set of inputs. Pretty simple, just renaming the tunnels and stuff.


![image](https://stasis.hackclub-assets.com/images/1774301700567-6enen6.png)

Looks a bit like this:


![image](https://stasis.hackclub-assets.com/images/1774301709842-z64lgf.png)

Really hyped. I think I'll convert this over to discrete compoenents before moving onto collision and points. I'm not entirely sure if I will just implement an adder or use an S-R latch there if I can. I'll figure that out soon.

**Total time spent: 0.25 hours**

# March 23, 9 PM: PADDLE GO BRR

Most of this session was just spend debugging the counter. Turns out I shouldn't be pulling the load pin high constantly, only when I need to load the value.. who knew? Not me since im pretty stupid :]

Regardless, I wired it up to a few 7 segments, and failed miserably, and reverted to ye faithful binary led thingy. And that helped me debug that. Once I realised that, I also realised this made my life significantly easier since I can handle the paddle going offscreen by just loading the max value when it exceeds a threshold. Pretty cool :]

![image](https://stasis.hackclub-assets.com/images/1774300351131-nemg8h.png)

I fixed the load issue by designing a quick and dirty S-R latch. It controls the main game pretty much. When a button is presed, it simulatenously sets the output to high, so it can start displaying hte paddle + loads the value. So two birds with one stone. Also the reset button just turns off the output. Three birds with one stone :]

Here's the diagram of me coming up with the bare logic:



![image](https://stasis.hackclub-assets.com/images/1774300370996-c288dd.png)


And here is me implementing said logic:



![image](https://stasis.hackclub-assets.com/images/1774300403751-27tihj.png)

Pretty cool. Outputs are reversed, I'll flip the not, I forgot to do that before writing this. Otherwise everythings great! Will duplicate this for the other paddle and tie it to the same S-R. Also haven't commited to the repo in like days, should probably do that before I inevitably screw something up majorly :]

**Total time spent: 1 hours**

# March 25, 11 AM: Discretifying the control logic

Converted this generic logic:


![image](https://stasis.hackclub-assets.com/images/1774439748381-uyu3ut.png)


Into some wiring that uses actual ICs, namely a 7408 and 7432.Neatly fits onto two fully populated ICs, very fun :]


![image](https://stasis.hackclub-assets.com/images/1774439881240-t7cqdg.png)

**Total time spent: 0.33 hours**

# March 25, 5 PM: paddle y counter

Spent some time rewiring the generic up/down paddle y counter with some nice little ICs. After some research, I settled on the 74191 U/D counter. Had to re-remember how to cascade them to get 10 bits (3*4bit > 10bits).


After that its was just some rewiring to debug my stupid mistakes, and a while spent till I realised I was wiring the output bits wrong (im my defence, why would they put d0 d2 and d3 on one side and d1 on the other side :| ).

Here is the first iteration of the wiring:

![image](https://stasis.hackclub-assets.com/images/1774459996032-4c9ovn.png)

Heres the final wiring:


![image](https://stasis.hackclub-assets.com/images/1774459924655-f6gsj2.png)

That was a lie, reading through this journal a few weeks later, I realised I forgot to journal a minor change I made. Basically just replacing that one OR there with an IC. That was it, here's what it looks like :]


![image](https://stasis.hackclub-assets.com/images/1775746187671-js8ulo.png)

**Total time spent: 1.5 hours**

# March 29, 2 PM: Shift Reg Shenanigans

Finally back at it. Trying to reduce the IC count that my current paddle logic would have from liek 12/paddle to something a bit more reasonable. Apparently the original one used shift registers, and so I'm doing that too. I would try and explain it here, but I'm not entirely convinced I understand it fully myself.

It starts with a 10 bit counter again, going into a 10bit comp, and then a dtype flip flop to make it a constant pulse output. And now that displays an entire scan line whenever it matches my y1 coords. For some reason its offset by a bit. I'm not sure why, I'll try and fix that I suppose:


![image](https://stasis.hackclub-assets.com/images/1774794795123-c34qu2.png)


![image](https://stasis.hackclub-assets.com/images/1774794806908-uy8wso.png)

Ok I'm still on this:

![image](https://stasis.hackclub-assets.com/images/1774795947194-mrqu0j.png)

hmm. Can't really fix it now, But I have somehwere to start from ig..

**Total time spent: 1 hours**

# March 30, 8 AM: More paddle rendering stuff :(

I was brainstorming using simple AND and S-R latches to generate the signal for it. But I don't think i can handle the vertical part without a 10 bit comparator, which will probalby chew threw a lot of propagation delay (of which I can tolerate about 40ns of). I might have to figure out how to properly use a shift register now, I don't think I can really get around that :|


![image](https://stasis.hackclub-assets.com/images/1774860444922-dxtp20.png)

I'll figure it out eventually, though I don't look forward to parsing the original schematics.


![image](https://stasis.hackclub-assets.com/images/1774860472866-tfz1gy.png)

**Total time spent: 1.33 hours**

# April 3, 9 AM: Finally Figuring out the Paddles

I decided to revist the idea of using latches and counters again, and after some doodling, I realised I would still need a few comps, but then I realised I can just use XNORs, since I was looking for equality anyways. And it gets rid of the need for an adder! Sick!


![image](https://stasis.hackclub-assets.com/images/1775208939776-9qvek3.png)

The x logic is simple since I'm just comparing to a constant, so I can just use a few ands. I threw that together and named the output pls to reflect my hopes and dreams :]


![image](https://stasis.hackclub-assets.com/images/1775209011045-5jp46t.png)


It did actually work on its own somehow.

After a bit of fiddling around, I implemented the y logic to. This is the one that relies on XNORs. XNORs truth table defines it to only be 1 when both inputs are equal, be that both 0 or both 1. So I used 10 of those to compare the current YCOUNT to the target Y coordinate.

![image](https://stasis.hackclub-assets.com/images/1775209174554-eqvz3m.png)


When it does equal that, I set a latch on the same pulse, and the ouput Q drives the pixel whilst enabling a counter, which will then count until 10 or 60 (x or y). The outputs go into an AND to see if its equal to 10 or 60 and then if it is, that feeds into the CLR of the counter and the RST of the latch, driving the pixel ouptput low! Neat.

I tied those two pixel outputs together with an AND and along with the master START/RESET latch, and voila, we have out pixel signal for the paddle!

The real fun of course comes in implementng this with discrete ICs. Not the most fun.

First implementation was somewhat close..




![image](https://stasis.hackclub-assets.com/images/1775209249409-zm55my.png)

![image](https://stasis.hackclub-assets.com/images/1775209290767-u9cn0s.png)

The counter was wrong I assume, but I went ahead and rewired it from scratch since I've noticed your wiring becomes exponentially better the more times you rewire the whole thing.

The next rewiring, while not any cleaner, did fix the issue (kinda):



![image](https://stasis.hackclub-assets.com/images/1775209320567-e1zxio.png)



![image](https://stasis.hackclub-assets.com/images/1775209330312-n5qbth.png)


It was just tied to the wrong output.

I rewired it one last time to be slightly more traceable:



![image](https://stasis.hackclub-assets.com/images/1775209383795-wd1a9d.png)

And then I got started with the Y logic block:



![image](https://stasis.hackclub-assets.com/images/1775209401111-6n8fss.png)

It sprawled a bit, and the paddle doesn't render yet, so somethings gone wrong somewhere, so I'll have to restart with the Y bit then :|. But regardless, massive progress!

Also I realised I can actually step down the clock speed to something more reasonable like 100MHz, which gives me a much more reasonable 100ns of Prop delay to work around, which I can do, over the 40 or so I was dealing with before. It does sacrifice VGA compliance, but who cares...

**Total time spent: 3.5 hours**

# April 5, 10 AM: Thonking

Really quick one, but I did some critical thonking, and realised something very important. Basically my current paddle dimensions are 60x10. Here's a thought, what if I change them to 64x16. I don't know why I didn't think of this before, but this makes my life *soo* much easier.

First I can simplify the counter logic. I don't have to check for a certain signal for the widht at all now, since the counter is 4 bits and can only count 0-15 anyways, so its reset is the exact width we want.


![image](https://stasis.hackclub-assets.com/images/1775385477826-s0arjs.png)

For the 64, we need 6 bits, which is less fun, but I can use 2 ICs there, and the rest becomes much simpler, I just need to comare to see if teh 6th bit = 1, and then I immediately clr, so I don't have to check all 6 bits.

I think with some more braining I can further reduce IC count. I was also fiddlign aroudn the idea of multiplexing the counter so I can just have 2  Y counters and 1 X counter between both paddles. I don't know how complicated the multiplexing logic would be, but shouldn't be horrendous right? We'll see. Im aiming for an IC count of &lt;18. I ran an analysis of other systems, and they're all fairly optimal:

 VGA Driver - 16ICs - reasonable considering the amount of logic needed

Y Coordinate Tracker - 9ICs between both Paddles. Tracks coordinates and changes them in turn with inputs. Really neat

Middle Parting - 2ICs, not really much less that can be done lol.

Actual Paddle Rendering  - 24ICs, just slightly atrocious (just slightly).

So yeah not out of the woods just yet, I want to get this down to somethign reasonable before I blow a bunch of money on getting 20 breadboards if I continue to let it spiral to like 100ICs lol.

Anyways here enjoy a sprawling picture of the current circuitry:




![image](https://stasis.hackclub-assets.com/images/1775385827269-yzd6ny.png)

**Total time spent: 0.25 hours**

# April 5, 10 AM: Discrete Paddles achieved!

A lot more brain-whacking later, I finally started to rebuild my logic from the start, building it out unit by unit and combing it with the old generic logic. Didn't take long into that process for me to realise that I was just using the wrong IC. I was uing the 74266 which is open-collector output, so it has to be connected to the collecter pin of a transisitor. After some digging, I found out that I had to use the 747266, and that would be solved immediately.


![image](https://stasis.hackclub-assets.com/images/1775384023215-n5npad.png)

So I wired that up, duplicated it for the second paddle, and did some IC minimisation by splitting one of the ANDs between both circuits. Still uses a crap-tonne of ICs, 24 total between them :|. At least the prop delay is decent :]

I also started working on discretifying this latch:



![image](https://stasis.hackclub-assets.com/images/1775384161624-c2m2u6.png)


Doesn't immediately work, I presume due it being active low instead of active high, so the startup behaviour is a bit spotty. I'll figure that out soon enough.

This is what our schematic looks like so far :]


![image](https://stasis.hackclub-assets.com/images/1775384218652-94w2hv.png)

Whats less :] is the fact that we're at 54 (or smth like that I might have miscoutned) ICs already, without implementing the ball or score tracking. Not :] very not :], in fact some might say its :[ or even :[[.

I'll see if theres any optimising I should be doing, but yeah. Yikes...

**Total time spent: 1.5 hours**

# April 5, 11 AM: Optimisation Magic!

Some very critical thonking later, I have discovered the mask.

I first redefined my frame:


![image](https://stasis.hackclub-assets.com/images/1775388770840-r3khlj.png)

And then realised since I am using clean powers, I can just ignore the bottom bits my using the top ones only!

Something like this:


![image](https://stasis.hackclub-assets.com/images/1775388808926-m2jzim.png)

The new frame composition is also reprecated immediatley, I realised switching from 10:60 to 16:64 would make it too square so I stretched it to 8:64, which is 1:8 yes, but doesn't look ugly imo, like 16:64 did (1:4).

It works pretty well already:


![image](https://stasis.hackclub-assets.com/images/1775388902495-5erlfa.png)


But it renders too high up to start with, not sure why entirely yet, and the movement is too clunky, since it updates in 64 pixel chunks lol. So I'll have to be less stingey with my Y logic, and probably use counters, but the x logic got so much simpler, literally just 7 XNORs and a 7 Input AND, which I suppose I can do with 2-3 ICs instead of the 5 o 6 I needed before!


![image](https://stasis.hackclub-assets.com/images/1775388996820-glulrz.png)

**Total time spent: 0.83 hours**

# April 5, 8 PM: More (-)Optimisation

Fixed the janky movement. Turns out I still needed my latch and counter for at least the Y, but in turn, I cut the AND for the logic and am just using the TC pin (or the 7th bit) to reset the latch and counter when it turns one. I also cut the 5 least significant bits, and now we have a much much more compact logic for the paddle rendering! Very fun :]


![image](https://stasis.hackclub-assets.com/images/1775391652982-cssig1.png)

Conveniently uses 12 XNORs, so I use 3 fully populated quad XNOR ICs! In turn, I am using a 6 bit counter, so 2 ICs for that. Only 1 latch per paddle, so only need 1 IC between both paddles. Similarly the final render output uses 2 ANDs, so I only need 1 IC between both for that bit. Things are falling into place. I could multiplex the counter between both of them since I don't need to use them simulatenously, but I'm not that depserate to minimise ICs just yet..

I started implenenting it using actual ICs, and it kinda works, but not really. Gotta rewire it again. I'll figure it out later :]


![image](https://stasis.hackclub-assets.com/images/1775420602482-dcmfcv.png)

**Total time spent: 1.5 hours**

# April 8, 11 AM: Debugging

I fixed it! Just a bit of debugging later I found that my X signal was plugged into the RESET pin instead of the Q output pin. That explained the weird rendering. After I realised that, it was just a matter of rewiring that section and voila!

Its not particularly symmetrical, but it works :]


![image](https://stasis.hackclub-assets.com/images/1775648739748-xkdqy8.png)


![image](https://stasis.hackclub-assets.com/images/1775648750982-ph4x09.png)

Here's the current sprawling city:


![image](https://stasis.hackclub-assets.com/images/1775648800138-3detmg.png)

We're not quite done with paddles just yet, I want to see if I can write some logic to prevent from going off screen, it has some weird behaviour, where it someties loops around and re-appears, but then disappears again before coming back properly after another loop. Its not the highest priority issues, but if I can solve it with a few ICs, it might be worth it.

**Total time spent: 0.33 hours**

# April 8, 11 AM: Implementing logic with ICs

I rushed the old implementation, and of course it doesn't work, so I'm working on it piece by piece.

First piece is peculiar...


![image](https://stasis.hackclub-assets.com/images/1775640398759-09bwq8.png)


The two paddles are linked and rendered across the screen.


![image](https://stasis.hackclub-assets.com/images/1775640416149-lcnny8.png)

I just forgot to wire the 10th bit into the NAND.


![image](https://stasis.hackclub-assets.com/images/1775640552705-vhmt5f.png)


![image](https://stasis.hackclub-assets.com/images/1775640560319-srzbv7.png)



So the first primary signal works, now I have to do the second signal, which has a lot more moving parts.

I figured out why my old logic wan't working, I was just messing something up with my latch and couter setting and resetting, since the generic compoenent I used had active HIGH inputs, but the ICs obviously are active LOW, and so that messed things up.


![image](https://stasis.hackclub-assets.com/images/1775641413603-rtttvs.png)


I fixed the generic logic to reflect the same, and also changed it to an 8bit counter and compare the 6th bit instead of using the overflow on a 6bit counter to make it more clear for me :]

And heres the final working version:
![image](https://stasis.hackclub-assets.com/images/1775642214514-1968uh.png)

Just got to extend it for the seecond paddle, but its much the same. Overall, I've reduced it from about 25ICs to 16ICs! Very exciting.


I got most of the second side wired up too, but ran into some issues when I ran it:


![image](https://stasis.hackclub-assets.com/images/1775646984250-92mfhu.png)


Not entirely sure why, but I'll figure it out :]

**Total time spent: 1.75 hours**

# April 9, 2 PM: Planning Ball Logic

With the Paddle logic done for the most part, and fully realised in ICs, its time to move onto the biggest hurdle so far, the ball!

The ball is obviously pretty important, and I want to realise it with as much detail as I can in its behaviour. Of course working with discrete logic has its limitations.

To prevent the persistent back and forth I had in designing and implementing and debugging and re-designing as I had with the paddle, I decided to outline my primary approach in more detail so I don't forget it when I have my sim open, and start working on some half thought out logic instead.

To begin with, the ball has a few quantifiable properties besides its physical appearance. It has its current X and Y position, its Vertical Direction (VDIR, Up or Down), its Horizontal Direction (HDIR, Left or Right) and the Angle (the angle it makes to a horizontal (or vertical if you want) line running through the balls centre).

The X and Y is simple, just use a 12bit U/D counter to track that (U/D since it has to move back and forth). VDIR and HDIR are even simpler, just use Latches, 1 for Up or Left and 0 for Down or Right. Neat. Now for the direction Vector, after some brainstroming I figured that since I'm using discrete logic, using a ratio between the Horizontal movement and the Vertical movement is probably the simpleset. And hey presto, I've invented the gradient!

But yeah its as simple as that in concept, you just update the Y and X counter in the integer ratio of the slope (2 up 3 to the right etc). Neat! So far I assumed a constant velocity, but I can totally change that by just increasing the clock speed for the ball. I'm still considering if I should run the ball and paddle logic on different clocks, it makes sense right now, since having a varying ball clock has its advantages. I'm not entirely sure how nicely that will later play with my collision logic, but so long as they're in the same neighbourhood, it should be fine.

Anyways here are my doodling notes as I figure this stuff out:


![image](https://stasis.hackclub-assets.com/images/1775745059572-k9fhm1.png)

![image](https://stasis.hackclub-assets.com/images/1775745070894-ecqk9m.png)

![image](https://stasis.hackclub-assets.com/images/1775745084228-te6orq.png)

![image](https://stasis.hackclub-assets.com/images/1775745096048-na40e7.png)

Just a matter of getting this working with generic logic blocks for now then!

**Total time spent: 1.5 hours**

# April 9, 4 PM: Documenting

Spent a couple minutes revising the documentation on the GitHub repo, and adding documentation for the newer sub-systems. Also revised project status. That was it!


![image](https://stasis.hackclub-assets.com/images/1775753455122-7epyp2.png)

(this wasn't the only addition, check out the repo if you want to see all changes added :] )

**Total time spent: 0.33 hours**

# April 9, 11 PM: Reworking Paddle Counters

Boy did I not expect to be doing this tonight. Basically I was getting started with implementing the start of the ball, when I was researching counters to use. I was pretty sure I would just use the 74191, but for reasons known only to me an hour and a half ago, I decided switching to the 74193 and also switching my old counter for the paddles to that as well.

Its been a long road getting there. I had to scrap my paddle ctrl logic entirely and rewire the entire module from scratch, for basically no reason. Though I do think I've managed to save an entire 2 ICs, and fully saturate all those used, which is basically 2.5 ICs saved! And it removes a pesky OR so I reduce unique ICs used for now too (I'll probably need an OR eventually but yay!)

The gist of the 74193 is that its dual clock, so I provide seperate clocks for the U and D. While wiring up a new controller, my directions didn't work until I put a NOT across them, and that works perfectly fine for whatever reason. I guess it cleans up the edge. But then when I put another NOT across it, its broken again? What?

Well regardless it works, and I trust that its just me being incompetent. This took far too long. Was not worth it. 2/10 experience. I'm hungry.

Anyways heres the before and after:

Before:

![image](https://stasis.hackclub-assets.com/images/1775776679230-9le8ie.png)

![image](https://stasis.hackclub-assets.com/images/1775776808803-lmtcre.png)

 (i couldn't find any better resolution pics lol)

After:


![image](https://stasis.hackclub-assets.com/images/1775776851615-90co2m.png)



Much cleaner (mainly cuz I used more tunnels, but logic is simpler too besides the convoluted paddle I suppose).

I also spent some time trying to fix this issue where when I start the sim, the START/RESET S-R latch that controls the RENDER enable signal settles randomly each time. I could easily fix this by using an RC network to pulse the RESET pin once after inital start up, but Digital is (believe it or not) a digital logic sim, and doesn't support Capcaitors ;-; . Very unfortunate. Maybe I should write my own logic sim like Digital but with analog stuff too :] . Yeah who am I kidding I doubt that will happen. It is now past midgnight, i am tired and rambling. goodnight. I regret this.

**Total time spent: 2 hours**

# April 10, 10 PM: Abandoning the rollover :]

Ok small update, this would require close to 8ICs. I am not sacraficing that much space :]

I messed around with just using the 10th bit, but thats 512, so it rollsover before stopping:


![image](https://stasis.hackclub-assets.com/images/1775861412151-arkk2v.png)


Welp this isn't happening right now at least. I can't really justify that IC count right now.

**Total time spent: 0.17 hours**

# April 10, 10 PM: Restricting paddle range

Started by debugging the bits for the Y value of my paddles using a simple LED output.


![image](https://stasis.hackclub-assets.com/images/1775856819048-pifkyl.png)

After some starting at flashing bits I realised I didn't want to solve the run-over thing. I'm not entirely sure why it behaves the way it does right now, but I have a slight hunch it might be something do with the VGA porches and stuff, but I'm not entirely convinced.

Either way I don't want to solve that so the cheap solution would be to just entirely disable the input entirely when its above and below a certain x value (top x-height or such)

I tried frankenstening some logic together:


![image](https://stasis.hackclub-assets.com/images/1775858803657-dsu1h9.png)


![image](https://stasis.hackclub-assets.com/images/1775858822899-yn4sk7.png)


![image](https://stasis.hackclub-assets.com/images/1775858831490-bk282t.png)

That didn't work on the first try.

Some more fiddling later, I'm doing an XNOR comparison to compare the paddle bits to 0. If its 0, I disable UP movement. The down movement is much more finicky. I'm trying to compare to the Y value to 416, which seems to get skipped over a lot. So I setup a basic 10 bit magnitude comparison, to make sure X>415 before I disable it. That seems to work a charm. I don't really want to do comparison for prop delay and just pure IC count. I'll try and figure out an efficient way of implementing this next.


![image](https://stasis.hackclub-assets.com/images/1775860060818-g6k4me.png)


![image](https://stasis.hackclub-assets.com/images/1775860072945-l8xscr.png)

Also random tidbit, I time tracked this using three 30 minute Shoot From The Hip show segments. Check them out on YT very fun :]

**Total time spent: 1.42 hours**

# May 7, 8 PM: Starting the Ball

Finally back to working on it after a long 3 week break. I got myself reaquainted somewhat quickly and jumped into it. I decided that the first goal was to have a moving ball. I can figure out collisions later.

I first implemented two counters for the ball's x and the ball's y coordinate, and set it up so that it can load the initial value, and it is loading from the x/yDir latches.


![image](https://stasis.hackclub-assets.com/images/1778184118696-ki97po.png)


I then made a crude render setup to generate the render signal. It uses 10 bit comparators and a load of 10 bit adds and subtracts lol, which won't fly in the actual setup, but for debugging the signal it works a charm.


![image](https://stasis.hackclub-assets.com/images/1778184716926-xk27xz.png)

First attempt had the ball rendering a bit off the target:


![image](https://stasis.hackclub-assets.com/images/1778184735476-kpk17y.png)

After a bit of debugging, my constants for the start position that I was loaded needed a bit of tweaking, as well as the rendering logic that I was running.



![image](https://stasis.hackclub-assets.com/images/1778185147109-gyksew.png)



![image](https://stasis.hackclub-assets.com/images/1778185157656-p4vulp.png)


I plugged it into the clock for a second, and what do you know, ball go brr :]


![image](https://stasis.hackclub-assets.com/images/1778185480158-y8hlwk.png)

The next bit is uncoupling the counters from the clock directly, and tying in a subsystem for velocity that provides the clock to track the ball.

My plan is to use registers. One reg will be the velocity, and that will tell how much to increment an accumulator by, and when that acumulator overflows, we'll have a tick to increment the coordinate. That gives us more granular control. I'll see if I decide to implement that later tonight, or if thats a tommorow or later this week thing :]

**Total time spent: 0.75 hours**

# May 18, 5 PM: Adding Accumulators to slow things down

Adding accumulators! Basically using a direct clock means we have literally no control of the speed, or for that matter the direction really, since the ratio of x:y movement would always be 1:1 (or some fixed ratio if we used multiple clocks). Obviously having the ball move in just one direction is just slightly boring... To remedy this pressing issue, we're using accumulators. 

The accumulators are just some 4 bit registers. One 4 bit register is just storing the x/y speed. The other 4 bit register acts as our accumulator. We dont' really care whats actually store in this reg, but more when it flowss over. We take the x/ySpeed, and and x/yAccumulator and add them with a 4 bit adder. The carry over of the adder acts as the clock, and the resultant sum or remainder is handed back to the accumulator. What this does in practice is lets me have much greater authority over the speed, whilst maintaing a fixed clock. I keep my clock as it was (well I bumped it up to 500 over 60Hz because we made our ball about 16 times slower), and load a speed into the x/ySpeed regs. Very fun!

Designing and implementing this sytem was the majority of today's session. Some other chores I did was adding a master render flag, so when a button is pressed, we enable render, using a latch, and also flash thel load pin on the x and y counters so we can reset the ball's position. The reason we need a master render (and master collsions), is beacuse since the clock is constantly ticking, the ball's coordinate is also constantly updating. Instead of figuring out a way to control the clock, I decided to add this flag to enable the rending + the collisions, so we don't really care what the ball's value is unless the render flag is high. Fun!

Anyways enough yap. Here are some juicy pictures:

The initial spaghetti implementation of the accumulators into the old counters and stuff. 
<img width="911" height="401" alt="image" src="https://github.com/user-attachments/assets/ba2ca1ef-b245-44a3-aa1c-d6aee9260932" />

Polished implementation of x and y accum + coordinates. very fun. just noticed that the labels are both x, will change that. also has the latches for directions and master render. lables are also goofed for those.

<img width="422" height="359" alt="image" src="https://github.com/user-attachments/assets/3918bda3-c501-4e68-8238-1a806d0ce7ac" />


**Total time spent: 1.00 hours**


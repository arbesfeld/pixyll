---
layout:     post
title:      Machine Building
image:      img/machine_header.jpg
number:     '13'
---

The assignment for this week was to build a machine. Luckily, this was a group project, so we had a whole team to tackle the various problems that come up with machine building.

My group decided to build a "magic" chess board -- a chess board that could automatically move pieces by using a magnet on the end of a 3-dimensional tool path. My team did a great job of building a very sturdy 3-axis machine:

{% include image.html url="/img/machine_mechanics.jpg" description="Close-up of a 2d joint on the machine." %}

And we have some amazing artists who created the chess board itself and the chess pieces:

{% include image.html url="/img/machine_board.jpg" description="" %}
{% include image.html url="/img/machine_pieces.jpg" description="" %}

### Coding

My role in the assignment was to code the tool path for the machine and the GUI that we used to control the chessboard. We used the [Gestalt framework](http:/http://mtm.cba.mit.edu/machines/science/) for sending (x, y, z) commands to the motor controllers. My only job was to send a tool path to the Gestalt nodes and create a GUI that could control the pieces.

Controlling a Gestalt machine in Python looks like this:

    # setup:
    self.xaAxisNode = nodes.networkedGestaltNode('XA Axis', self.fabnet, filename = '086-005a.py', persistence = self.persistence)
    self.xbAxisNode = nodes.networkedGestaltNode('XB Axis', self.fabnet, filename = '086-005a.py', persistence = self.persistence)
    self.yAxisNode = nodes.networkedGestaltNode('Y Axis', self.fabnet, filename = '086-005a.py', persistence = self.persistence)
    self.zAxisNode = nodes.networkedGestaltNode('Z Axis', self.fabnet, filename = '086-005a.py', persistence = self.persistence)

    self.xyzNode = nodes.compoundNode(self.xaAxisNode, self.xbAxisNode, self.yAxisNode, self.zAxisNode)

    # moving:
    self.stages.move([x,y,z], 0)

I found an exiting Python chess GUI and called a "move_piece" function whenever a piece was moved

    # seq is a sequence of (x, y) coordinates to move along
    def move_piece(self, seq):
        (xFirst, yFirst) = self.transform_pos(seq[0])
        self.move([xFirst, xFirst, yFirst, 15]) # move to the starting position
        for move in seq:
            (x, y) = self.transform_pos(move)
            self.move([x, x, y, 0]) # move along the path
        (xLast, yLast) = self.transform_pos(seq[-1])
        self.move([xLast, xLast, yLast, 15]) # move down away from the pieces

And there were special cases for capturing pieces and moving knights.

Here is a video of the whole machine working together:

<iframe src="//player.vimeo.com/video/114145321" width="750" height="420" frameborder="0" webkitallowfullscreen mozallowfullscreen allowfullscreen></iframe>
<br>
All in all, I was really happy with the final outcome. It was a lot of fun to work with the whole team!





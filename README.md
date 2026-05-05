# spider-moving-project-using-python
#I make this spider moving project using python..
import numpy as np
import matplotlib.pyplot as plt
import matplotlib.patches as patches
from matplotlib.animation import FuncAnimation
from IPython.display import HTML

fig, ax = plt.subplots(figsize=(8, 6))
ax.set_xlim(0, 10)
ax.set_ylim(0, 8)
ax.set_facecolor('black')
fig.patch.set_facecolor('black')
ax.axis('off')

# Spider state
state = {'x': 5.0, 'y': 4.0, 'vx': 0.05, 'vy': 0.03, 'frame': 0}
artists = []

def draw_spider(ax, x, y, frame):
    items = []
    swing = np.sin(frame * 0.3) * 0.3

    # Leg pairs
    leg_angles = [40, 70, 110, 140]
    for angle in leg_angles:
        for side in [1, -1]:
            a = np.radians(angle * side + swing * 20 * side)
            kx = x + np.cos(a) * 0.5
            ky = y + np.sin(a) * 0.5
            ex = kx + np.cos(a - np.radians(30) * side) * 0.4
            ey = ky + np.sin(a - np.radians(30) * side) * 0.4
            line, = ax.plot([x, kx, ex], [y, ky, ey], color='#888888', lw=1.5)
            items.append(line)

    # Abdomen
    abdomen = plt.Circle((x, y + 0.45), 0.3, color='#1a1a1a', zorder=3)
    abdomen_ring = plt.Circle((x, y + 0.45), 0.3, color='#444444', fill=False, lw=1, zorder=4)
    ax.add_patch(abdomen)
    ax.add_patch(abdomen_ring)
    items += [abdomen, abdomen_ring]

    # Body
    body = plt.Circle((x, y), 0.22, color='#222222', zorder=5)
    body_ring = plt.Circle((x, y), 0.22, color='#555555', fill=False, lw=1, zorder=6)
    ax.add_patch(body)
    ax.add_patch(body_ring)
    items += [body, body_ring]

    # Eyes
    for ex_off in [-0.08, 0.08]:
        eye = plt.Circle((x + ex_off, y + 0.08), 0.04, color='red', zorder=7)
        ax.add_patch(eye)
        items.append(eye)

    return items

def update(frame):
    global artists
    for a in artists:
        a.remove()

    state['x'] += state['vx']
    state['y'] += state['vy']
    state['frame'] = frame

    if state['x'] > 9.5 or state['x'] < 0.5:
        state['vx'] *= -1
    if state['y'] > 7.5 or state['y'] < 0.5:
        state['vy'] *= -1

    artists = draw_spider(ax, state['x'], state['y'], frame)
    return artists

ani = FuncAnimation(fig, update, frames=200, interval=50, blit=True)
plt.close()
HTML(ani.to_jshtml())

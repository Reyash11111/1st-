import numpy as np
from PIL import Image
import trimesh
import matplotlib.pyplot as plt

# Load grayscale image and resize
input_image_path = "0c099a76-0b9f-496a-9567-405c7bbbeb40.png" 
image = Image.open(input_image_path).convert("L")
image = image.resize((128, 128))  # smaller size for speed
image_np = np.array(image)

# Normalize pixel values to range [0, 1]
height_map = image_np / 255.0

# Show original grayscale image
plt.imshow(image_np, cmap='gray')
plt.title("Grayscale Image (Height Map)")
plt.axis('off')
plt.show()

# Create vertices grid (x, y)
x = np.linspace(0, 1, height_map.shape[1])
y = np.linspace(0, 1, height_map.shape[0])
x, y = np.meshgrid(x, y)

# Flatten arrays
vertices = np.column_stack((
    x.flatten(),                
    y.flatten(),                
    height_map.flatten()         
))

# Create faces 
faces = []
rows, cols = height_map.shape
for i in range(rows - 1):
    for j in range(cols - 1):
        idx = i * cols + j
        # Triangle 1
        faces.append([idx, idx + 1, idx + cols])
        # Triangle 2
        faces.append([idx + 1, idx + cols + 1, idx + cols])

faces = np.array(faces)

# Create mesh
mesh = trimesh.Trimesh(vertices=vertices, faces=faces)

# Export mesh
mesh.export("grayscale_3d_model.obj")
print("3D model saved as 'grayscale_3d_model.obj'")

# Display mesh inline (interactive)
mesh.show()

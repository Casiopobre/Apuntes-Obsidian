### Triangulos:
Código con `GL_TRIANGLES`:
```c
glBegin(GL_TRIANGLES);
	glColor3f (.909f, .180f, .462f); // Cor
	glVertex3f(0.5f, -0.5f, 0.5f); // V1
	glVertex3f(0.5f, 0.5f, 0.5f); // V2
	glVertex3f(-0.5f, -0.5f, 0.5f); // V3
	
	glColor3f (1.0f, .340f, .662f); // Cor
	glVertex3f(-0.5f, -0.5f, 0.5f); // V4
	glVertex3f(0.5f, 0.5f, 0.5f); // V5
	glVertex3f(-0.5f, 0.5f, 0.5f); // V6
glEnd();
```

Código con `GL_TRIANGLE_STRIP`:
```c
glBegin(GL_TRIANGLES);
	glColor3f (.909f, .180f, .462f); // Cor
	glVertex3f(0.5f, -0.5f, 0.5f); // V1
	glVertex3f(0.5f, 0.5f, 0.5f); // V2
	glVertex3f(-0.5f, -0.5f, 0.5f); // V3
	
	glColor3f (1.0f, .340f, .662f); // Cor
	glVertex3f(-0.5f, 0.5f, 0.5f); // V6
glEnd();
```

Output:
![[ex_triangles_output.png| center | 200]]
Con `GL_TRIANGLE_FAN`:
```c
glBegin(GL_TRIANGLE_FAN);
	glColor3f (.909f, .180f, .462f); // Color 1
	glVertex3f( 0.0f,  0.0f, 0.5f); // Centro (V0)
	glVertex3f( 0.5f, -0.5f, 0.5f); // V1
	glVertex3f( 0.5f,  0.5f, 0.5f); // V2
	
	glColor3f (.4f, .180f, .462f); // Color 2
	glVertex3f(-0.5f,  0.5f, 0.5f); // V3
	
	glColor3f (.596f, .411f, .941f); // Color 3
	glVertex3f(-0.5f, -0.5f, 0.5f); // V4
	
	glColor3f (.317f, .690f, .960f); // Color 4
	glVertex3f( 0.5f, -0.5f, 0.5f); // Pecha o ciclo (V2)
glEnd();
```

Output:
![[ex_triangle_fan_output.png| center | 200]]


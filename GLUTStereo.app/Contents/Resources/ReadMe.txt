GLUTStereo

NOTE: To evaluate this example correctly a display capable of 96Hz or better vertical
retrace is required. Typically a MultiSync type CRT display.

1.2 (3/04/08):	Update to Xcode 3.0
				Other bug fixes 
				
1.1 (2/20/03): Adds the ability to switch in and out of stereo mode and better
			   support.

An examples showing how to set up and use stereo with 
GLUT under Mac OS X v10.2.3 or later.

GLUT stereo support requires setting a stereo pixel format
then using glutGameMode to get a full screen stereo drawable.
Stereo in a window is not supported at this time.  Below is 
an example of this set up:

    glutInit(&argc, (char **)argv);
    // stereo display mode for glut
    glutInitDisplayMode(GLUT_DOUBLE | GLUT_RGB | GLUT_DEPTH | GLUT_STEREO); 
	glutGameModeString("1024x768:32@120"); // must now use full screen game mode
	// enter gamemode to get stereo context 
	// (may get invalid drawable warnings in console, 
	//  this is normal and will be fixed in the future)
	glutEnterGameMode(); 

Once the stereo context is set up the application simply
renders into the left and right back buffer and allows the
driver to pick the correct front buffer to display.

To correct render stereo one should use the parallel axis asymmetric 
frustum perspective projection method as shown in this example.  A 
good source of information on this technique is Paul Bourke's
web site at <http://astronomy.swin.edu.au/~pbourke/opengl/stereogl/>

A number of example surfaces used are by Roger Bagula with Paul Bourke 
contributing some of the surface rendering evaluation code.  More 
renderings of these and other surfaces can be found at 
<http://astronomy.swin.edu.au/~pbourke/surfaces/>. 

Surface rendering is has the simple, yet very effective on Mac OS X v10.2 and later,
optimization of being compiled in display lists in this example, though the required 
use of two sided lighting likely will slow the rendering down.

Also, shown in this example is a simple, yet rotationally correct,
track ball algorithm with was originally adapted from the NSGL Teapot
example and converted to standard C code.

Note: to view stereo one must use stereo glasses this example is
designed to support stereographics glasses which require a blue
sync line at the bottom of the screen (in lieu of hardware syncing).
The code below is an example of how to draw the blue sync line.

void DrawBlueLine(GLint window_width, GLint window_height)
{
	GLint i;
	unsigned long buffer;
	
	glPushAttrib(GL_ALL_ATTRIB_BITS);
	
	glDisable(GL_ALPHA_TEST);
	glDisable(GL_BLEND);
	for(i = 0; i < 6; i++) glDisable(GL_CLIP_PLANE0 + i);
	glDisable(GL_COLOR_LOGIC_OP);
	glDisable(GL_COLOR_MATERIAL);
	glDisable(GL_DEPTH_TEST);
	glDisable(GL_DITHER);
	glDisable(GL_FOG);
	glDisable(GL_LIGHTING);
	glDisable(GL_LINE_SMOOTH);
	glDisable(GL_LINE_STIPPLE);
	glDisable(GL_SCISSOR_TEST);
	glDisable(GL_SHARED_TEXTURE_PALETTE_EXT);
	glDisable(GL_STENCIL_TEST);
	glDisable(GL_TEXTURE_1D);
	glDisable(GL_TEXTURE_2D);
	glDisable(GL_TEXTURE_3D);
	glDisable(GL_TEXTURE_CUBE_MAP);
	glDisable(GL_TEXTURE_RECTANGLE_EXT);
	glDisable(GL_VERTEX_PROGRAM_ARB);
		
	for(buffer = GL_BACK_LEFT; buffer <= GL_BACK_RIGHT; buffer++) {
		GLint matrixMode;
		GLint vp[4];
		
		glDrawBuffer(buffer);
		
		glGetIntegerv(GL_VIEWPORT, vp);
		glViewport(0, 0, window_width, window_height);
		
		glGetIntegerv(GL_MATRIX_MODE, &matrixMode);
		glMatrixMode(GL_PROJECTION);
		glPushMatrix();
		glLoadIdentity();
		
		glMatrixMode(GL_MODELVIEW);
		glPushMatrix();
		glLoadIdentity();
		glScalef(2.0f / window_width, -2.0f / window_height, 1.0f);
		glTranslatef(-window_width / 2.0f, -window_height / 2.0f, 0.0f);
	
		// draw sync lines
		glColor3d(0.0f, 0.0f, 0.0f);
		glBegin(GL_LINES); // Draw a background line
			glVertex3f(0.0f, window_height - 0.5f, 0.0f);
			glVertex3f(window_width, window_height - 0.5f, 0.0f);
		glEnd();
		glColor3d(0.0f, 0.0f, 1.0f);
		glBegin(GL_LINES); // Draw a line of the correct length 
		                   // (the cross over is about 40% across the screen from the left)
			glVertex3f(0.0f, window_height - 0.5f, 0.0f);
			if(buffer == GL_BACK_LEFT)
				glVertex3f(window_width * 0.30f, window_height - 0.5f, 0.0f);
			else
				glVertex3f(window_width * 0.80f, window_height - 0.5f, 0.0f);
		glEnd();
	
		glPopMatrix();
		glMatrixMode(GL_PROJECTION);
		glPopMatrix();
		glMatrixMode(matrixMode);
		
		glViewport(vp[0], vp[1], vp[2], vp[3]);
	}	
	glPopAttrib();
}

void DrawBlueLine_Simple(GLint window_width, GLint window_height)
{
	unsigned long buffer;
	
	for(buffer = GL_BACK_LEFT; buffer <= GL_BACK_RIGHT; buffer++) {
		GLint matrixMode;
		GLint vp[4];
		
		glDrawBuffer(buffer);
		
		glGetIntegerv(GL_VIEWPORT, vp);
		glViewport(0, 0, window_width, window_height);
		
		glGetIntegerv(GL_MATRIX_MODE, &matrixMode);
		glMatrixMode(GL_PROJECTION);
		glPushMatrix();
		glLoadIdentity();
		
		glMatrixMode(GL_MODELVIEW);
		glPushMatrix();
		glLoadIdentity();
		glScalef(2.0f / window_width, -2.0f / window_height, 1.0f);
		glTranslatef(-window_width / 2.0f, -window_height / 2.0f, 0.0f);
	
		// draw sync lines
		glColor3d(0.0f, 0.0f, 0.0f);
		glBegin(GL_LINES); // Draw a background line
			glVertex3f(0.0f, window_height - 0.5f, 0.0f);
			glVertex3f(window_width, window_height - 0.5f, 0.0f);
		glEnd();
		glColor3d(0.0f, 0.0f, 1.0f);
		glBegin(GL_LINES); // Draw a line of the correct length (the cross over is about 40% across the screen from the left
			glVertex3f(0.0f, window_height - 0.5f, 0.0f);
			if(buffer == GL_BACK_LEFT)
				glVertex3f(window_width * 0.30f, window_height - 0.5f, 0.0f);
			else
				glVertex3f(window_width * 0.80f, window_height - 0.5f, 0.0f);
			glVertex3f(window_width, window_height - 0.5f, 0.0f);
		glEnd();
	
		glPopMatrix();
		glMatrixMode(GL_PROJECTION);
		glPopMatrix();
		glMatrixMode(matrixMode);
		
		glViewport(vp[0], vp[1], vp[2], vp[3]);
	}
}



Documentation Author: Niko Procopi 2019

This tutorial was designed for Visual Studio 2017 / 2019
If the solution does not compile, retarget the solution
to a different version of the Windows SDK. If you do not
have any version of the Windows SDK, it can be installed
from the Visual Studio Installer Tool

Welcome to the Anisotropy Tutorial!
Prerequesites: Mipmapping

Comparison Screenshot:
Left:   texture + normal map
Middle: texture + normal map + mipmapping
Right:  texture + normal map + anisotropy

Anisotropy is meant to replace the old usage of mipmapping
Anisotropy requires more GPU power than traditional mipmapping,
and it was used sparingly in the Xbox 360 days.
Today, any modern computer can handle anisotropy without any difficulty,
	and traditional mipmapping is used significantly less often

Very important:
Anisotropy still uses uses mipmaps, but in a very different way than 
the previous tutorial (which was how mipmaps were traditionally used)

We will create what is called a "Texture Sampler". A Sampler is what shaders use
to get pixels from textures. Up until now, we have been using a default Sampler that
is built-in to OpenGL. Now we will make our own sampler that uses Anisotropy

This tutorial is also easier than most tutorials, but a little 
harder than mipmapping. Just go to Texture.cpp and follow these
steps. Create a sampler around line 27 of Texture.cpp, put it between the #include
and the Texture::Texture constructer, so that one sampler can be used for all 
textures (which is enough for a simple tutorial).
	GLuint sampler;
In the Texture constructer, we generate our texture like this:
	glGenTextures(1, &m_texture);
Now we need to generate our sampler. We will only have one sampler, so we
only need to initialize the sampler one time
	if (sampler == 0)
		glGenSamplers(1, &sampler);
We already bind the texture like this:
	glBindTexture(GL_TEXTURE_2D, m_texture);
Now we have to bind our sampler:
	glBindSampler(m_texture, sampler);
After we set the Texture Parameter for GL_TEXTURE_WRAP_S and GL_TEXTURE_WRAP_T,
we were setting GL_TEXTURE_MAG_FILTER and GL_TEXTURE_MIN_FILTER as texture 
parameters, but we won't be doing that anymore
Remove these lines:
	glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MAG_FILTER, GL_LINEAR);
	glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MIN_FILTER, GL_LINEAR_MIPMAP_LINEAR);
From now on, we won't be setting MIN_FILTER and MAG_FILTER in the texture's parameter,
we will be moving that to the sampler's parameter
	glSamplerParameteri(sampler, GL_TEXTURE_MAG_FILTER, GL_LINEAR);
	glSamplerParameteri(sampler, GL_TEXTURE_MIN_FILTER, GL_LINEAR_MIPMAP_LINEAR);
Then we generate the mipmaps like usual:
	glGenerateMipmap(GL_TEXTURE_2D);
Now it is time to enable Anisotropy. OpenGL makes it easy for us to enable.
Anisotropy has a level of usage from 0 to 100. GPUs usually don't support 100 levels.
We can use an OpenGL command to get the maximum level supported (usually 16, which
is still great), by using this command:
	GLfloat maxAniso = 0.0f;
	glGetFloatv(GL_MAX_TEXTURE_MAX_ANISOTROPY_EXT, &maxAniso);
Then we apply the maxAniso variable to the level that we want to use in our sampler:
	glSamplerParameterf(sampler, GL_TEXTURE_MAX_ANISOTROPY_EXT, maxAniso);
And that's it, you're ready to go

How to improve:
Create a Sampler class, so that any sampler can be attached to any texture (no tutorial)
Try MSAA (Multi-sample anti-aliasing) to remove pixelation from images (really easy, two lines) (see tutorial)
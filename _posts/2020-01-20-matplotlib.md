---
laytout: post
---

## installation
```bash
$ python3 -m pip install --user matplotlib
```

## notes

plotting a simple line graph is actually quite easy employing `matplotlib`. After installation, 
1. _`import matplotlib.pyplot as plt`_ (pyplot module provides many functions that generate charts and plots); 

2. declare input and output values (on x and y axis);

3. Before generate the plot, you can choose a built-in style for the plot: _`plt.style.use('xxxx')`_ 

   ```python
   >>> plt.style.available
   ['seaborn-dark', 'seaborn-darkgrid', 'seaborn-ticks', 'fivethirtyeight', 'seaborn-whitegrid', 'classic', '_classic_test', 'fast', 'seaborn-talk', 'seaborn-dark-palette', 'seaborn-bright', 'seaborn-pastel', 'grayscale', 'seaborn-notebook', 'ggplot', 'seaborn-colorblind', 'seaborn-muted', 'seaborn', 'Solarize_Light2', 'seaborn-paper', 'bmh', 'tableau-colorblind10', 'seaborn-white', 'dark_background', 'seaborn-poster', 'seaborn-deep']
   ```

   

4. _`fig, ax = plt.subplots()`_ (subplots function can generate one or more plots in the same figure, _fig->the_whole_figure, ax->a_plot_in_figure_);

5. _`ax.axis([x_from, x_to, y_from, y_to])`_ setting axis range

6. Methods to render data:

   1. Plot a single line: _`ax.plot(input, output, lineweight=x)`_
   2. Plot dots: _`ax.scatter(x, y, s=number, c='color_name'/(0.3, 0.8, 1))`_
   3. 

7. style the plot i.e. the _ax_, by setting its *title* and *x/y labels*, *tick_params*

```python
ax.set_title("Square Numbers", fontsize=24)
ax.set_xlabel("Value", fontsize=14)
ax.set_ylabel("Square of Value", fontsize=14)
ax.tick_params(axis='both', labelsize=14)
```



6. _`plt.show()`_ (opens matplotlib's viewer)


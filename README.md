# Retrofit
Retrofit library implementation with themoviedb url

MOVIE URL = "http://api.themoviedb.org/3/movie/top_rated?<--!api-key-->";



Getting TMDB API Key / Sample JSON

For this tutorial we will use The TMDb API. In order to use this API it is necessary to obtain the API key. Here you can take a look how to obtain the API key. In short, you need to register and login in order to obtain the key.

1. Create a new project in Android Studio from File ⇒ New Project. When it prompts you to select the default activity, select Empty Activity and proceed.

2. Open build.gradle and add Retrofit, Gson dependencies.
        
   dependencies {
    ...........
    ...........
    ...........

    // retrofit, gson
    compile 'com.google.code.gson:gson:2.6.2'
    compile 'com.squareup.retrofit2:retrofit:2.0.2'
    compile 'com.squareup.retrofit2:converter-gson:2.0.2'
   }

3. Since we are working with network operations we need to add INTERNET permissions in AndroidManifest.xml file
   
   <uses-permission android:name="android.permission.INTERNET"/>

4. Create four sub packages named activity, adapter, rest and model in your main package. Move your MainActivity under activity package.

5. Generate class from 'http://www.jsonschema2pojo.org/' using URL

6. Create a class named Movie.java under model package.


***********Movie.java************
*********************************
package com.ark.retrofitmovie.model;
import java.util.List;
import com.google.gson.annotations.Expose;
import com.google.gson.annotations.SerializedName;
/**
 * Created by noufal on 28/11/17.
 */
public class Movie {
    @SerializedName("vote_count")
    @Expose
    private Integer voteCount;
    @SerializedName("id")
    @Expose
    private Integer id;
    @SerializedName("video")
    @Expose
    private Boolean video;
    @SerializedName("vote_average")
    @Expose
    private Double voteAverage;
    @SerializedName("title")
    @Expose
    private String title;
    @SerializedName("popularity")
    @Expose
    private Double popularity;
    @SerializedName("poster_path")
    @Expose
    private String posterPath;
    @SerializedName("original_language")
    @Expose
    private String originalLanguage;
    @SerializedName("original_title")
    @Expose
    private String originalTitle;
    @SerializedName("genre_ids")
    @Expose
    private List<Integer> genreIds = null;
    @SerializedName("backdrop_path")
    @Expose
    private String backdropPath;
    @SerializedName("adult")
    @Expose
    private Boolean adult;
    @SerializedName("overview")
    @Expose
    private String overview;
    @SerializedName("release_date")
    @Expose
    private String releaseDate;

    public Integer getVoteCount() {
        return voteCount;
    }

    public void setVoteCount(Integer voteCount) {
        this.voteCount = voteCount;
    }

    public Integer getId() {
        return id;
    }

    public void setId(Integer id) {
        this.id = id;
    }

    public Boolean getVideo() {
        return video;
    }

    public void setVideo(Boolean video) {
        this.video = video;
    }

    public Double getVoteAverage() {
        return voteAverage;
    }

    public void setVoteAverage(Double voteAverage) {
        this.voteAverage = voteAverage;
    }

    public String getTitle() {
        return title;
    }

    public void setTitle(String title) {
        this.title = title;
    }

    public Double getPopularity() {
        return popularity;
    }

    public void setPopularity(Double popularity) {
        this.popularity = popularity;
    }

    public String getPosterPath() {
        return posterPath;
    }

    public void setPosterPath(String posterPath) {
        this.posterPath = posterPath;
    }

    public String getOriginalLanguage() {
        return originalLanguage;
    }

    public void setOriginalLanguage(String originalLanguage) {
        this.originalLanguage = originalLanguage;
    }

    public String getOriginalTitle() {
        return originalTitle;
    }

    public void setOriginalTitle(String originalTitle) {
        this.originalTitle = originalTitle;
    }

    public List<Integer> getGenreIds() {
        return genreIds;
    }

    public void setGenreIds(List<Integer> genreIds) {
        this.genreIds = genreIds;
    }

    public String getBackdropPath() {
        return backdropPath;
    }

    public void setBackdropPath(String backdropPath) {
        this.backdropPath = backdropPath;
    }

    public Boolean getAdult() {
        return adult;
    }

    public void setAdult(Boolean adult) {
        this.adult = adult;
    }

    public String getOverview() {
        return overview;
    }

    public void setOverview(String overview) {
        this.overview = overview;
    }

    public String getReleaseDate() {
        return releaseDate;
    }

    public void setReleaseDate(String releaseDate) {
        this.releaseDate = releaseDate;
    }
}
****************************************************



7. Also we need to create MovieResponse.java class, since we have some extra fields like page number. This class contains all fetched movies and extra information. Create MovieResponse.java under model package.



***********MovieResponse.java************
*****************************************
package com.ark.retrofitmovie.model;
import java.util.List;
import com.google.gson.annotations.Expose;
import com.google.gson.annotations.SerializedName;
/**
 * Created by noufal on 28/11/17.
 */
public class MoviesResponse {
    @SerializedName("page")
    @Expose
    private Integer page;
    @SerializedName("total_results")
    @Expose
    private Integer totalResults;
    @SerializedName("total_pages")
    @Expose
    private Integer totalPages;
    @SerializedName("results")
    @Expose
    private List<Movie> results = null;

    public Integer getPage() {
        return page;
    }

    public void setPage(Integer page) {
        this.page = page;
    }

    public Integer getTotalResults() {
        return totalResults;
    }

    public void setTotalResults(Integer totalResults) {
        this.totalResults = totalResults;
    }

    public Integer getTotalPages() {
        return totalPages;
    }

    public void setTotalPages(Integer totalPages) {
        this.totalPages = totalPages;
    }

    public List<Movie> getResults() {
        return results;
    }

    public void setResults(List<Movie> results) {
        this.results = results;
    }
}
*****************************************









8. To send network requests to an API, we need to use the Retrofit Builder class and specify the base URL for the service. So, create a class named ApiClient.java under rest package.

Here BASE_URL – it is basic URL of our API. We will use this URL for all requests later.




****************ApiClient.java***********************
----------------------------------------------------
import retrofit2.Retrofit;
import retrofit2.converter.gson.GsonConverterFactory; 
public class ApiClient { 
    public static final String BASE_URL = "http://api.themoviedb.org/3/";
    private static Retrofit retrofit = null;
 
 
    public static Retrofit getClient() {
        if (retrofit==null) {
            retrofit = new Retrofit.Builder()
                    .baseUrl(BASE_URL)
                    .addConverterFactory(GsonConverterFactory.create())
                    .build();
        }
        return retrofit;
    }
}
*********************************************************************





The endpoints are defined inside of an interface using special retrofit annotations to encode details about the parameters and request method. In addition, the return value is always a parameterized Call<T> object such as Call<MovieResponse>. For instance, the interface defines each endpoint in the following way.

9. Create ApiInterface.java under rest package.





****************************ApiInterface.java********************
-----------------------------------------------------------------
package com.ark.retrofitmovie.rest;
import com.ark.retrofitmovie.model.MoviesResponse;
import retrofit2.Call;
import retrofit2.http.GET;
import retrofit2.http.Path;
import retrofit2.http.Query;
/**
 * Created by noufal on 28/11/17.
 */
public interface ApiInterface {
    @GET("movie/top_rated")
    Call<MoviesResponse> getTopRatedMovies(@Query("api_key") String apiKey);

    @GET("movie/{id}")
    Call<MoviesResponse> getMovieDetails(@Path("id") int id, @Query("api_key") String apiKey);
}
********************************************************************
        
        

Each endpoint specifies an annotation of the HTTP method (GET, POST, etc.) and the parameters of this method can also have special annotations (@Query, @Path, @Body etc.)

Take a look to other annotations:

@Path – variable substitution for the API endpoint. For example movie id will be swapped for{id} in the URL endpoint.

@Query – specifies the query key name with the value of the annotated parameter.

@Body – payload for the POST call

@Header – specifies the header with the value of the annotated parameter




10. Let’s make the first request from our MainActivity. If we want to consume the API asynchronously, we call the service as follows. Open the MainActivity.java and do the below changes.

Make sure that you replaced API_KEY with yours.





******************MainActivity.java*************************
-----------------------------------------------------------
public class MainActivity extends AppCompatActivity {
    private static final String TAG = MainActivity.class.getSimpleName(); 
    // TODO - insert your themoviedb.org API KEY here
    private final static String API_KEY = ""; 
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
 
        if (API_KEY.isEmpty()) {
            Toast.makeText(getApplicationContext(), "Please obtain your API KEY first from themoviedb.org", Toast.LENGTH_LONG).show();
            return;
        }
 
        ApiInterface apiService =
                ApiClient.getClient().create(ApiInterface.class);
 
        Call<MoviesResponse> call = apiService.getTopRatedMovies(API_KEY);
        call.enqueue(new Callback<MoviesResponse>() {
            @Override
            public void onResponse(Call<MoviesResponse>call, Response<MoviesResponse> response) {
                List<Movie> movies = response.body().getResults();
                Log.d(TAG, "Number of movies received: " + movies.size());
            }
 
            @Override
            public void onFailure(Call<MoviesResponse>call, Throwable t) {
                // Log error here since request failed
                Log.e(TAG, t.toString());
            }
        });
    }
}
**************************************************************







Retrofit will download and parse the API data on a background thread, and then return the results back to the UI thread via the onResponse or onFailure method.

Congratulations! We have created our first rest client. Let’s create some UI in order to see our results

11. Let’s create ListView for fetched results. We will use RecyclerView for it. First of all, add it to the gradle.gradle




build.gradle
dependencies {
    .
    .
 
    // recycler view
    compile 'com.android.support:recyclerview-v7:23.3.0'
}







In order to show fetched items we need to create layout, which will show all data. We need 4 TextView and 1 ImageView for star image.

12. Open colors.xml and add the below color values.






colors.xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <color name="colorPrimary">#3F51B5</color>
    <color name="colorPrimaryDark">#303F9F</color>
    <color name="colorAccent">#FF4081</color>
 
    <color name="orange">#FF3909</color>
    <color name="colorAccentDark">#00B482</color>
 
    <color name="colorBlack">#555555</color>
    <color name="colorWhite">#FFFFFF</color>
    <color name="colorGrey">#707070</color>
    <color name="colorGreyLight">#8A8A8A</color>
</resources>







13. Create a layout named star.xml under res drawable with the below content.







star.xml
<!-- drawable/star.xml -->
<vector xmlns:android="http://schemas.android.com/apk/res/android"
    android:width="24dp"
    android:height="24dp"
    android:viewportHeight="24"
    android:viewportWidth="24">
    <path
        android:fillColor="#000"
        android:pathData="M12,17.27L18.18,21L16.54,13.97L22,9.24L14.81,8.62L12,2L9.19,8.62L2,9.24L7.45,13.97L5.82,21L12,17.27Z" />
</vector>







14. Create a layout named list_item_movie.xml under res layout.







list_item_movie.xml
<?xml version="1.0" encoding="utf-8"?>

<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/movies_layout"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:gravity="center_vertical"
    android:minHeight="72dp"
    android:orientation="horizontal"
    android:padding="16dp">
    <ImageView
        android:id="@+id/movie_thumb"
        android:layout_width="10dp"
        android:layout_height="80dp"
        android:layout_weight="0.3" />

    <LinearLayout
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:layout_weight="1"
        android:orientation="vertical">

        <TextView
            android:id="@+id/title"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_gravity="top"
            android:paddingRight="16dp"
            android:textColor="@color/colorBlack"
            android:textSize="16sp"
            android:textStyle="bold" />


        <TextView
            android:id="@+id/subtitle"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:paddingRight="16dp"
            android:textColor="@color/colorGreyLight" />

        <TextView
            android:id="@+id/description"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:maxLines="3"
            android:paddingRight="16dp"
            android:textColor="@color/colorGreyLight" />

    </LinearLayout>

    <LinearLayout
        android:layout_width="wrap_content"
        android:layout_height="35dp"
        android:orientation="horizontal">

        <ImageView
            android:id="@+id/rating_image"
            android:layout_width="15dp"
            android:layout_height="15dp"
            android:layout_centerInParent="true"
            android:scaleType="centerCrop"
            android:src="@drawable/star"
            android:tint="@color/colorAccent" />


        <TextView
            android:id="@+id/rating"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_marginLeft="8dp"
            android:text="5.0" />
    </LinearLayout>

</LinearLayout>








15. Adapter is a common pattern which helps to bind view and data, so let’s implement adapter for this. Create a class named MoviesAdapter.java under adapter package.









***************MoviesAdapter.java***********************
---------------------------------------------------------
package com.ark.retrofitmovie.adapter;
import android.content.Context;
import android.graphics.drawable.Drawable;
import android.support.v7.widget.RecyclerView;
import android.util.Log;
import android.view.LayoutInflater;
import android.view.View;
import android.view.ViewGroup;
import android.widget.ImageView;
import android.widget.LinearLayout;
import android.widget.TextView;

import com.ark.retrofitmovie.R;
import com.ark.retrofitmovie.model.Movie;
import com.squareup.picasso.Picasso;

import java.util.List;

import static android.content.ContentValues.TAG;
import static com.ark.retrofitmovie.rest.ApiClient.IMAGE_URL;
/**
 * Created by noufal on 28/11/17.
 */
public class MoviesAdapter extends RecyclerView.Adapter<MoviesAdapter.MovieViewHolder> {
    private List<Movie> movies;
    private int rowLayout;
    private Context context;


    public static class MovieViewHolder extends RecyclerView.ViewHolder {
        ImageView movieThumb;
        LinearLayout moviesLayout;
        TextView movieTitle;
        TextView data;
        TextView movieDescription;
        TextView rating;


        public MovieViewHolder(View v) {
            super(v);
            movieThumb = (ImageView) v.findViewById(R.id.movie_thumb);
            moviesLayout = (LinearLayout) v.findViewById(R.id.movies_layout);
            movieTitle = (TextView) v.findViewById(R.id.title);
            data = (TextView) v.findViewById(R.id.subtitle);
            movieDescription = (TextView) v.findViewById(R.id.description);
            rating = (TextView) v.findViewById(R.id.rating);
        }
    }

    public MoviesAdapter(List<Movie> movies, int rowLayout, Context context) {
        this.movies = movies;
        this.rowLayout = rowLayout;
        this.context = context;
    }

    @Override
    public MoviesAdapter.MovieViewHolder onCreateViewHolder(ViewGroup parent,
                                                            int viewType) {
        View view = LayoutInflater.from(parent.getContext()).inflate(rowLayout, parent, false);
        return new MovieViewHolder(view);
    }


    @Override
    public void onBindViewHolder(MovieViewHolder holder, final int position) {
        Picasso.with(this.context)
                .load(IMAGE_URL + movies.get(position).getPosterPath())
                .into(holder.movieThumb);
        holder.movieTitle.setText(movies.get(position).getTitle());
        holder.data.setText(movies.get(position).getReleaseDate());
        holder.movieDescription.setText(movies.get(position).getOverview());
        holder.rating.setText(movies.get(position).getVoteAverage().toString());
    }

    @Override
    public int getItemCount() {
        return movies.size();
    }
}
**************************************************************














16. Open MainActivity.java and modify the code as below.


















*******************MainActivity.java************************
------------------------------------------------------------

package com.ark.retrofitmovie.activity;

import android.support.v7.app.AppCompatActivity;
import android.os.Bundle;
import android.support.v7.widget.LinearLayoutManager;
import android.support.v7.widget.RecyclerView;
import android.util.Log;
import android.widget.Toast;

import com.ark.retrofitmovie.R;
import com.ark.retrofitmovie.adapter.MoviesAdapter;
import com.ark.retrofitmovie.model.Movie;
import com.ark.retrofitmovie.model.MoviesResponse;
import com.ark.retrofitmovie.rest.ApiClient;
import com.ark.retrofitmovie.rest.ApiInterface;

import java.util.List;

import retrofit2.Call;
import retrofit2.Callback;
import retrofit2.Response;

public class MainActivity extends AppCompatActivity {
    private static final String TAG = MainActivity.class.getSimpleName();

    private final static String API_KEY = "7e8f60e325cd06e164799af1e317d7a7";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        if (API_KEY.isEmpty()) {
            Toast.makeText(getApplicationContext(), "Please obtain your API KEY first from themoviedb.org", Toast.LENGTH_LONG).show();
            return;
        }

        final RecyclerView recyclerView = (RecyclerView) findViewById(R.id.movies_recycler_view);
        recyclerView.setLayoutManager(new LinearLayoutManager(this));

        ApiInterface apiService = ApiClient.getClient().create(ApiInterface.class);

        Call<MoviesResponse> call = apiService.getTopRatedMovies(API_KEY);

        call.enqueue(new Callback<MoviesResponse>() {
            @Override
            public void onResponse(Call<MoviesResponse> call, Response<MoviesResponse> response) {
                int statusCode = response.code();
                List<Movie> movies = response.body().getResults();
                Log.d(TAG, "Number of movies received" + movies.size());
                recyclerView.setAdapter(new MoviesAdapter(movies, R.layout.list_item_movie,getApplicationContext()));
            }

            @Override
            public void onFailure(Call<MoviesResponse> call, Throwable t) {
                Log.e(TAG, t.toString());
            }
        });

    }
}

************************************************************************










#############    Thank You    ###################
